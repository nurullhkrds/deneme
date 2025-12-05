public static String prepareSqlStatement2(GetOrderedBillsSummaryListCriteria searchCriteria) {

    boolean hasBillInfoList = searchCriteria.getBillInfoDTOList() != null
            && !searchCriteria.getBillInfoDTOList().isEmpty();

    boolean hasOrderIdList = searchCriteria.getOrderIdList() != null
            && !searchCriteria.getOrderIdList().isEmpty();

    String dynamicBlock1 = buildDynamicBlock1(hasBillInfoList, hasOrderIdList);
    String dynamicBlock2 = buildDynamicBlock2(hasBillInfoList, hasOrderIdList);

    String sql = """
            SELECT A.*,
                   (
                     SELECT COUNT(*)
                       FROM BILL.PAYABLE_BILL PB
                       JOIN BILL.SUBSCRIBER S
                         ON PB.INSTITUTION_ID          = S.INSTITUTION_ID
                        AND PB.INSTITUTION_DEBT_TYPE_ID = S.INSTITUTION_DEBT_TYPE_ID
                        AND PB.SUBSCRIBER_NO           = S.SUBSCRIBER_NO
                       JOIN BILL.PAYMENT_ORDER PO
                         ON PO.SUBSCRIBER_ID = S.ID
                       JOIN BILL.INSTITUTION I
                         ON I.ID = PB.INSTITUTION_ID
                        AND I.ID = S.INSTITUTION_ID
                      WHERE PB.STATUS = 'OVERDUE'
                        AND PO.STATUS = 'ORDERED'
                        AND PB.BILL_DUE_DATE BETWEEN TRUNC(SYSDATE) - 30 AND TRUNC(SYSDATE)
                        AND EXISTS (
                              SELECT 1
                                FROM BILL.INSTITUTION_ORDER_PYM_METHOD IOPM
                               WHERE IOPM.INSTITUTION_ID = PB.INSTITUTION_ID
                                 AND IOPM.ORDER_METHOD IN ('ACCOUNT_LIST', 'CREDIT_CARD', 'GENERAL_ACCOUNT')
                                 AND IOPM.IS_ACTIVE = 1
                        )
                        /*DYNAMIC_BLOCK_1*/
                   ) AS COUNT_OF_NOTPAID_BILLS
              FROM (
                    SELECT
                      CASE
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK'
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ'
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'CEPTEL'   THEN 'CEPTEL'
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'SU'       THEN 'SU'
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM'
                        ELSE 'DİĞER'
                      END AS PRODUCT,
                      NVL(SUM(PAID_AND_OVERDUE_BILLS_AMOUNTS.AMOUNT), 0) AS PRODUCT_BASE_AMOUNT
                    FROM (
                          -- ÖDENMİŞ FATURALAR (PAYMENT - PAID)
                          SELECT
                            P2.PAYMENT_AMOUNT AS AMOUNT,
                            I3.PRODUCT_CODE   AS PRODUCT_CODE,
                            TO_CHAR(I3.PRODUCT_CODE || '#' || I3.INSTITUTION_CODE || '#' || P2.SUBSCRIBER_NO) AS INFO_KEY
                          FROM BILL.PAYMENT P2
                          JOIN BILL.INSTITUTION I3
                            ON P2.INSTITUTION_ID = I3.ID
                         WHERE P2.CUSTOMER_NO = :CUSTOMER_NO
                           AND P2.STATUS      = 'PAID'
                           AND P2.CURRENCY    = 'YTL'
                           AND TO_CHAR(P2.PAYMENT_DATE, 'YYYYMM') = TO_CHAR(SYSDATE, 'YYYYMM')
                           AND I3.PRODUCT_CODE NOT IN ('BELEDİYE')
            
                          UNION ALL
            
                          -- SİPARİŞ VERİLMİŞ GECİKMİŞ FATURALAR (PAYABLE_BILL - OVERDUE & ORDERED)
                          SELECT
                            PB2.AMOUNT        AS AMOUNT,
                            I3.PRODUCT_CODE   AS PRODUCT_CODE,
                            TO_CHAR(I3.PRODUCT_CODE || '#' || I3.INSTITUTION_CODE || '#' || S2.SUBSCRIBER_NO) AS INFO_KEY
                          FROM BILL.PAYABLE_BILL PB2
                          JOIN BILL.INSTITUTION I3
                            ON I3.ID = PB2.INSTITUTION_ID
                          JOIN BILL.SUBSCRIBER S2
                            ON PB2.INSTITUTION_ID          = S2.INSTITUTION_ID
                           AND PB2.INSTITUTION_DEBT_TYPE_ID = S2.INSTITUTION_DEBT_TYPE_ID
                           AND PB2.SUBSCRIBER_NO           = S2.SUBSCRIBER_NO
                          JOIN BILL.PAYMENT_ORDER PO2
                            ON PO2.SUBSCRIBER_ID = S2.ID
                           AND PO2.CUSTOMER_NO   = :CUSTOMER_NO
                         WHERE PB2.STATUS = 'OVERDUE'
                           AND PO2.STATUS = 'ORDERED'
                    ) PAID_AND_OVERDUE_BILLS_AMOUNTS
                   WHERE 1 = 1
                   /*DYNAMIC_BLOCK_2*/
                   GROUP BY CASE
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK'
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ'
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'CEPTEL'   THEN 'CEPTEL'
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'SU'       THEN 'SU'
                        WHEN PAID_AND_OVERDUE_BILLS_AMOUNTS.PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM'
                        ELSE 'DİĞER'
                   END
              ) A
            """;

    sql = sql.replace("/*DYNAMIC_BLOCK_1*/", dynamicBlock1);
    sql = sql.replace("/*DYNAMIC_BLOCK_2*/", dynamicBlock2);

    return sql;
}


private static String buildDynamicBlock1(boolean hasBillInfoList, boolean hasOrderIdList) {

    if (!hasBillInfoList && !hasOrderIdList) {
        return "";
    }

    StringBuilder sb = new StringBuilder();
    sb.append(" AND TO_CHAR(I.PRODUCT_CODE || '#' || I.INSTITUTION_CODE || '#' || S.SUBSCRIBER_NO) IN (");

    boolean first = true;

    if (hasBillInfoList) {
        sb.append("SELECT * FROM TABLE(SYS.ODCIVARCHAR2LIST(:BILL_INFO_LIST))");
        first = false;
    }

    if (hasOrderIdList) {
        if (!first) sb.append(" UNION ");
        sb.append("""
                SELECT TO_CHAR(I2.PRODUCT_CODE || '#' || I2.INSTITUTION_CODE || '#' || S2.SUBSCRIBER_NO)
                  FROM BILL.PAYMENT_ORDER POR2
                  JOIN BILL.SUBSCRIBER    S2 ON POR2.SUBSCRIBER_ID = S2.ID
                  JOIN BILL.INSTITUTION   I2 ON I2.ID = S2.INSTITUTION_ID
                 WHERE POR2.ID IN (:ORDER_ID_LIST)
                """);
    }

    sb.append(")");

    return sb.toString();
}

private static String buildDynamicBlock2(boolean hasBillInfoList, boolean hasOrderIdList) {

    if (!hasBillInfoList && !hasOrderIdList) {
        return "";
    }

    StringBuilder sb = new StringBuilder();
    sb.append(" AND INFO_KEY IN (");

    boolean first = true;

    if (hasBillInfoList) {
        sb.append("SELECT * FROM TABLE(SYS.ODCIVARCHAR2LIST(:BILL_INFO_LIST))");
        first = false;
    }

    if (hasOrderIdList) {
        if (!first) sb.append(" UNION ");
        sb.append("""
                SELECT TO_CHAR(I4.PRODUCT_CODE || '#' || I4.INSTITUTION_CODE || '#' || S4.SUBSCRIBER_NO)
                  FROM BILL.PAYMENT_ORDER POR3
                  JOIN BILL.SUBSCRIBER    S4 ON POR3.SUBSCRIBER_ID = S4.ID
                  JOIN BILL.INSTITUTION   I4 ON I4.ID = S4.INSTITUTION_ID
                 WHERE POR3.ID IN (:ORDER_ID_LIST)
                """);
    }

    sb.append(")");

    return sb.toString();
}



