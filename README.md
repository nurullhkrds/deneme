public static String prepareSqlStatement2(GetOrderedBillsSummaryListCriteria searchCriteria) {

    // --- Dinamik liste flag'leri ---
    boolean hasBillInfoList = searchCriteria.getBillInfoList() != null
            && !searchCriteria.getBillInfoList().isEmpty();

    boolean hasOrderIdList = searchCriteria.getOrderIdList() != null
            && !searchCriteria.getOrderIdList().isEmpty();

    StringBuilder sqlUnPaid = new StringBuilder();

    sqlUnPaid.append("SELECT A.*, ");
    sqlUnPaid.append("       (SELECT COUNT(*) ");
    sqlUnPaid.append("          FROM BILL.PAYMENT P ");
    sqlUnPaid.append("          JOIN BILL.SUBSCRIBER S ");
    sqlUnPaid.append("            ON P.INSTITUTION_ID = S.INSTITUTION_ID ");
    sqlUnPaid.append("           AND P.INSTITUTION_DEBT_TYPE_ID = S.INSTITUTION_DEBT_TYPE_ID ");
    sqlUnPaid.append("           AND P.SUBSCRIBER_NO = S.SUBSCRIBER_NO ");
    sqlUnPaid.append("          JOIN BILL.PAYMENT_ORDER PO ");
    sqlUnPaid.append("            ON PO.SUBSCRIBER_ID = S.ID ");
    sqlUnPaid.append("           AND PO.CUSTOMER_NO = P.CUSTOMER_NO ");
    sqlUnPaid.append("          JOIN BILL.INSTITUTION I ");
    sqlUnPaid.append("            ON I.ID = P.INSTITUTION_ID ");
    sqlUnPaid.append("           AND I.ID = S.INSTITUTION_ID ");
    sqlUnPaid.append("         WHERE P.STATUS = 'OVERDUE' ");
    sqlUnPaid.append("           AND P.BILL_DUE_DATE BETWEEN TRUNC(SYSDATE) - 30 AND TRUNC(SYSDATE) ");
    sqlUnPaid.append("           AND EXISTS ( ");
    sqlUnPaid.append("                 SELECT 1 ");
    sqlUnPaid.append("                   FROM BILL.INSTITUTION_ORDER_PYM_METHOD IOPM ");
    sqlUnPaid.append("                  WHERE IOPM.INSTITUTION_ID = P.INSTITUTION_ID ");
    sqlUnPaid.append("                    AND IOPM.ORDER_METHOD IN ('ACCOUNT_LIST', 'CREDIT_CARD', 'GENERAL_ACCOUNT') ");
    sqlUnPaid.append("               ) ");

    // === 1. DİNAMİK BLOK: I / P için IN(...) filtresi ===
    appendBillInfoOrderFilterForPayment(sqlUnPaid, hasBillInfoList, hasOrderIdList);

    sqlUnPaid.append("       ) COUNT_OF_NOTPAID_BILLS ");
    sqlUnPaid.append("  FROM ( ");
    sqlUnPaid.append("        SELECT CASE ");
    sqlUnPaid.append("                 WHEN I3.PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK' ");
    sqlUnPaid.append("                 WHEN I3.PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ' ");
    sqlUnPaid.append("                 WHEN I3.PRODUCT_CODE = 'CEPTEL'    THEN 'CEPTEL' ");
    sqlUnPaid.append("                 WHEN I3.PRODUCT_CODE = 'SU'       THEN 'SU' ");
    sqlUnPaid.append("                 WHEN I3.PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM' ");
    sqlUnPaid.append("                 ELSE 'DİĞER' ");
    sqlUnPaid.append("               END AS PRODUCT, ");
    sqlUnPaid.append("               NVL(SUM(P2.PAYMENT_AMOUNT), 0) PRODUCT_BASE_AMOUNT ");
    sqlUnPaid.append("          FROM BILL.PAYMENT P2 ");
    sqlUnPaid.append("          JOIN BILL.INSTITUTION I3 ");
    sqlUnPaid.append("            ON P2.INSTITUTION_ID = I3.ID ");
    sqlUnPaid.append("         WHERE P2.CUSTOMER_NO = :CUSTOMER_NO ");
    sqlUnPaid.append("           AND P2.STATUS IN ('PAID', 'OVERDUE') ");
    sqlUnPaid.append("           AND P2.CURRENCY = 'YTL' ");
    sqlUnPaid.append("           AND (TO_CHAR(P2.PAYMENT_DATE, 'YYYYMM') = TO_CHAR(SYSDATE, 'YYYYMM') ");
    sqlUnPaid.append("                OR P2.PAYMENT_DATE IS NULL) ");
    sqlUnPaid.append("           AND I3.PRODUCT_CODE NOT IN ('BELEDİYE') ");

    // === 2. DİNAMİK BLOK: I3 / P2 için IN(...) filtresi ===
    appendBillInfoOrderFilterForPayment2(sqlUnPaid, hasBillInfoList, hasOrderIdList);

    sqlUnPaid.append("         GROUP BY CASE ");
    sqlUnPaid.append("                     WHEN I3.PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK' ");
    sqlUnPaid.append("                     WHEN I3.PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ' ");
    sqlUnPaid.append("                     WHEN I3.PRODUCT_CODE = 'CEPTEL'    THEN 'CEPTEL' ");
    sqlUnPaid.append("                     WHEN I3.PRODUCT_CODE = 'SU'       THEN 'SU' ");
    sqlUnPaid.append("                     WHEN I3.PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM' ");
    sqlUnPaid.append("                     ELSE 'DİĞER' ");
    sqlUnPaid.append("                  END ");
    sqlUnPaid.append("       ) A ");

    // Senin halihazırda kullandığın dinamik condition (alias'a dikkat etmen gerekebilir)
    addCondition(sqlUnPaid, "P.CUSTOMER_NO", searchCriteria.getCustomerNo(), "CUSTOMER_NO");

    return sqlUnPaid.toString();
}

/**
 * 1. alt sorgu için (PAYMENT P, INSTITUTION I) dinamik IN(...) filtresi.
 */
private static void appendBillInfoOrderFilterForPayment(StringBuilder sql,
                                                        boolean hasBillInfoList,
                                                        boolean hasOrderIdList) {

    // Her iki liste de boşsa bu filtreyi hiç ekleme (tüm kayıtlar gelir).
    // Eğer hiç kayıt gelmesin istersen, buraya:
    //   sql.append(" AND 1 = 0 ");
    // yazıp return edebilirsin.
    if (!hasBillInfoList && !hasOrderIdList) {
        return;
    }

    sql.append("           AND TO_CHAR(I.PRODUCT_CODE || '#' || I.INSTITUTION_CODE || '#' || P.SUBSCRIBER_NO) IN (");

    boolean first = true;

    // BILL_INFO_LIST doluysa ekle
    if (hasBillInfoList) {
        sql.append("SELECT * FROM TABLE(SYS.ODCIVARCHAR2LIST(:BILL_INFO_LIST))");
        first = false;
    }

    // ORDER_ID_LIST doluysa ekle
    if (hasOrderIdList) {
        if (!first) {
            sql.append(" UNION ");
        }
        sql.append("SELECT TO_CHAR(I2.PRODUCT_CODE || '#' || I2.INSTITUTION_CODE || '#' || S2.SUBSCRIBER_NO) COLUMN_VALUE ");
        sql.append("  FROM BILL.PAYMENT_ORDER POR2, ");
        sql.append("       BILL.SUBSCRIBER    S2, ");
        sql.append("       BILL.INSTITUTION   I2 ");
        sql.append(" WHERE POR2.SUBSCRIBER_ID = S2.ID ");
        sql.append("   AND I2.ID = S2.INSTITUTION_ID ");
        sql.append("   AND POR2.ID IN (:ORDER_ID_LIST)");
    }

    sql.append(") ");
}

/**
 * 2. alt sorgu için (PAYMENT P2, INSTITUTION I3) dinamik IN(...) filtresi.
 */
private static void appendBillInfoOrderFilterForPayment2(StringBuilder sql,
                                                         boolean hasBillInfoList,
                                                         boolean hasOrderIdList) {

    if (!hasBillInfoList && !hasOrderIdList) {
        return;
    }

    sql.append("           AND TO_CHAR(I3.PRODUCT_CODE || '#' || I3.INSTITUTION_CODE || '#' || P2.SUBSCRIBER_NO) IN (");

    boolean first = true;

    if (hasBillInfoList) {
        sql.append("SELECT * FROM TABLE(SYS.ODCIVARCHAR2LIST(:BILL_INFO_LIST))");
        first = false;
    }

    if (hasOrderIdList) {
        if (!first) {
            sql.append(" UNION ");
        }
        sql.append("SELECT TO_CHAR(I4.PRODUCT_CODE || '#' || I4.INSTITUTION_CODE || '#' || S3.SUBSCRIBER_NO) COLUMN_VALUE ");
        sql.append("  FROM BILL.PAYMENT_ORDER POR3, ");
        sql.append("       BILL.SUBSCRIBER    S3, ");
        sql.append("       BILL.INSTITUTION   I4 ");
        sql.append(" WHERE POR3.SUBSCRIBER_ID = S3.ID ");
        sql.append("   AND I4.ID = S3.INSTITUTION_ID ");
        sql.append("   AND POR3.ID IN (:ORDER_ID_LIST)");
    }

    sql.append(") ");
}
