public static String prepareSqlStatement2(GetOrderedBillsSummaryListCriteria searchCriteria) {

    boolean hasBillInfoList = searchCriteria.getBillInfoList() != null
            && !searchCriteria.getBillInfoList().isEmpty();

    boolean hasOrderIdList = searchCriteria.getOrderIdList() != null
            && !searchCriteria.getOrderIdList().isEmpty();

    String dynamicBlock1 = buildDynamicBlock1(hasBillInfoList, hasOrderIdList);
    String dynamicBlock2 = buildDynamicBlock2(hasBillInfoList, hasOrderIdList);

    String sql = """
            SELECT A.*,
                   (SELECT count(*)
                      FROM BILL.PAYMENT P
                      JOIN BILL.SUBSCRIBER S
                        ON P.INSTITUTION_ID = S.INSTITUTION_ID
                       AND P.INSTITUTION_DEBT_TYPE_ID = S.INSTITUTION_DEBT_TYPE_ID
                       AND P.SUBSCRIBER_NO = S.SUBSCRIBER_NO
                      JOIN BILL.PAYMENT_ORDER PO
                        ON PO.SUBSCRIBER_ID = S.ID
                       AND PO.CUSTOMER_NO = P.CUSTOMER_NO
                      JOIN BILL.INSTITUTION I
                        ON I.ID = P.INSTITUTION_ID
                       AND I.ID = S.INSTITUTION_ID
                     WHERE P.STATUS = 'OVERDUE'
                       AND P.BILL_DUE_DATE BETWEEN TRUNC(SYSDATE) - 30 AND TRUNC(SYSDATE)
                       AND EXISTS
                         (SELECT 1
                            FROM BILL.INSTITUTION_ORDER_PYM_METHOD IOPM
                           WHERE IOPM.INSTITUTION_ID = P.INSTITUTION_ID
                             AND IOPM.ORDER_METHOD IN ('ACCOUNT_LIST', 'CREDIT_CARD', 'GENERAL_ACCOUNT'))
            
                       /*DYNAMIC_BLOCK_1*/
            
                   ) COUNT_OF_NOTPAID_BILLS
            
              FROM (SELECT CASE
                             WHEN I3.PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK'
                             WHEN I3.PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ'
                             WHEN I3.PRODUCT_CODE = 'CEPTEL'   THEN 'CEPTEL'
                             WHEN I3.PRODUCT_CODE = 'SU'       THEN 'SU'
                             WHEN I3.PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM'
                             ELSE 'DİĞER'
                           END AS PRODUCT,
                           NVL(SUM(P2.PAYMENT_AMOUNT), 0) PRODUCT_BASE_AMOUNT
            
                      FROM BILL.PAYMENT P2
                      JOIN BILL.INSTITUTION I3
                        ON P2.INSTITUTION_ID = I3.ID
                     WHERE P2.CUSTOMER_NO = :CUSTOMER_NO
                       AND P2.STATUS IN ('PAID', 'OVERDUE')
                       AND P2.CURRENCY = 'YTL'
                       AND (TO_CHAR(P2.PAYMENT_DATE, 'YYYYMM') = TO_CHAR(SYSDATE, 'YYYYMM')
                         OR P2.PAYMENT_DATE IS NULL)
                       AND I3.PRODUCT_CODE NOT IN ('BELEDİYE')
            
                       /*DYNAMIC_BLOCK_2*/
            
                     GROUP BY CASE
                                WHEN I3.PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK'
                                WHEN I3.PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ'
                                WHEN I3.PRODUCT_CODE = 'CEPTEL'   THEN 'CEPTEL'
                                WHEN I3.PRODUCT_CODE = 'SU'       THEN 'SU'
                                WHEN I3.PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM'
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
        return ""; // tamamen boş bırak → filtre yok
    }

    StringBuilder sb = new StringBuilder();
    sb.append(" AND TO_CHAR(I.PRODUCT_CODE || '#' || I.INSTITUTION_CODE || '#' || P.SUBSCRIBER_NO) IN (");

    boolean first = true;

    if (hasBillInfoList) {
        sb.append("SELECT * FROM TABLE(SYS.ODCIVARCHAR2LIST(:BILL_INFO_LIST))");
        first = false;
    }

    if (hasOrderIdList) {
        if (!first) sb.append(" UNION ");
        sb.append("""
                SELECT TO_CHAR(I2.PRODUCT_CODE || '#' || I2.INSTITUTION_CODE || '#' || S2.SUBSCRIBER_NO)
                  FROM BILL.PAYMENT_ORDER POR2,
                       BILL.SUBSCRIBER    S2,
                       BILL.INSTITUTION   I2
                 WHERE POR2.SUBSCRIBER_ID = S2.ID
                   AND I2.ID = S2.INSTITUTION_ID
                   AND POR2.ID IN (:ORDER_ID_LIST)
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
    sb.append(" AND TO_CHAR(I3.PRODUCT_CODE || '#' || I3.INSTITUTION_CODE || '#' || P2.SUBSCRIBER_NO) IN (");

    boolean first = true;

    if (hasBillInfoList) {
        sb.append("SELECT * FROM TABLE(SYS.ODCIVARCHAR2LIST(:BILL_INFO_LIST))");
        first = false;
    }

    if (hasOrderIdList) {
        if (!first) sb.append(" UNION ");
        sb.append("""
                SELECT TO_CHAR(I4.PRODUCT_CODE || '#' || I4.INSTITUTION_CODE || '#' || S3.SUBSCRIBER_NO)
                  FROM BILL.PAYMENT_ORDER POR3,
                       BILL.SUBSCRIBER    S3,
                       BILL.INSTITUTION   I4
                 WHERE POR3.SUBSCRIBER_ID = S3.ID
                   AND I4.ID = S3.INSTITUTION_ID
                   AND POR3.ID IN (:ORDER_ID_LIST)
                """);
    }

    sb.append(")");

    return sb.toString();
}


