package com.ykb.baas.transaction.bill.transformer;

import org.apache.commons.lang3.StringUtils;
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;

public class GetOrderedBillListTransformer {

    public static String prepareSqlStatement(FetchBaasOrderedDebtsSearchCriteria searchCriteria) {

        StringBuilder sql = new StringBuilder("""
                SELECT DISTINCT
                    I.PRODUCT_CODE AS PRODUCT,
                    I.INSTITUTION_CODE AS INSTITUTION,
                    S.SUBSCRIBER_NO,
                    P.SUBSCRIBER_NAME,
                    P.BILL_NO,
                    P.BILL_DUE_DATE,
                    P.BILL_ISSUE_DATE,
                    P.BILL_TERM,
                    P.AMOUNT AS BILL_AMOUNT,
                    P.PAYMENT_AMOUNT AS BILL_PAYMENT_AMOUNT,
                    P.CURRENCY,
                    P.STATUS,
                    P.PAYMENT_METHOD,
                    P.CONTRACT_NO,
                    P.BRANCH_CODE,
                    P.CHANNEL_CODE,
                    PN.NOTIFICATION_STATUS AS PAYMENT_NOTIFICATION_STATUS,
                    P.CUSTOMER_NO,
                    P.ACCOUNT_NO,
                    P.CREDIT_CARD_NO,
                    P.PAYMENT_DATE,
                    P.PAYMENT_TIME,
                    P.ADDITIONAL_INFO_1,
                    P.ADDITIONAL_INFO_2,
                    P.ADDITIONAL_INFO_3,
                    P.ADDITIONAL_INFO_4,
                    P.ADDITIONAL_INFO_5,
                    P.ADDITIONAL_INFO_6,
                    P.ADDITIONAL_INFO_7,
                    P.ADDITIONAL_INFO_8,
                    P.ADDITIONAL_INFO_9,
                    PO.CREATE_DATE,
                    PO.ORDER_METHOD,
                    IAO.RECEIPT_CODE
                FROM BILL.PAYMENT_ORDER PO
                JOIN BILL.PAYMENT_ORDER_DETAIL POD  ON PO.ID = POD.ORDER_ID
                JOIN BILL.SUBSCRIBER S               ON PO.SUBSCRIBER_ID = S.ID
                JOIN BILL.INSTITUTION I              ON S.INSTITUTION_ID = I.ID
                JOIN BILL.INSTITUTION_DEBT_TYPE IBT  ON IBT.ID = S.INSTITUTION_DEBT_TYPE_ID AND IBT.INSTITUTION_ID = S.INSTITUTION_ID
                JOIN BILL.PAYMENT P                  ON P.INSTITUTION_ID = I.ID
                                                    AND P.INSTITUTION_DEBT_TYPE_ID = IBT.ID
                                                    AND P.CUSTOMER_NO = PO.CUSTOMER_NO
                                                    AND P.SUBSCRIBER_NO = S.SUBSCRIBER_NO
                JOIN BILL.PAYMENT_NOTIFICATION PN    ON PN.ID = (
                        SELECT ID FROM BILL.PAYMENT_NOTIFICATION
                        WHERE PAYMENT_ID = P.ID
                          AND NOTIFICATION_TYPE = 'INST_PAYMENT'
                        ORDER BY CREATE_DATE DESC
                        FETCH FIRST 1 ROW ONLY
                )
                JOIN BILL.INSTITUTION_ACCOUNTING_INFO IAO ON I.ID = IAO.INSTITUTION_ID
                WHERE PO.STATUS = 'ORDERED'
                """);

        // ------ FİLTRELER (1. BÖLÜM) ------

        addCondition(sql, "P.CUSTOMER_NO", searchCriteria.getCustomerNo(), "CUSTOMER_NO");
        addCondition(sql, "I.PRODUCT_CODE", searchCriteria.getProductCode(), "PRODUCT_CODE");
        addCondition(sql, "I.INSTITUTION_CODE", searchCriteria.getInstitutionCode(), "INSTITUTION_CODE");
        addCondition(sql, "P.STATUS", searchCriteria.getStatus(), "STATUS");
        addCondition(sql, "PO.ORDER_METHOD", searchCriteria.getOrderMethod(), "ORDER_METHOD");

        if (searchCriteria.getQueryStartDate() != null && searchCriteria.getQueryEndDate() != null) {
            sql.append(" AND P.BILL_DUE_DATE BETWEEN :QUERY_START_DATE AND :QUERY_END_DATE\n");
        }

        if (StringUtils.isNotEmpty(searchCriteria.getAccountNo())) {
            sql.append("""
                    AND :ACCOUNT_NO IN (
                        SELECT ACCOUNT_NO FROM BILL.PAYMENT_ORDER_DETAIL POD
                        WHERE POD.ORDER_ID = PO.ID AND POD.SEQUENCE_NO IN (0,1)
                    )
                    AND :ACCOUNT_NO IN (
                        SELECT ACCOUNT_NO FROM BILL.GENERAL_ACCOUNT GA
                        WHERE GA.CUSTOMER_NO = PO.CUSTOMER_NO AND GA.SEQUENCE_NO = 1
                    )
                    """);
        }

        addCondition(sql, "P.CREDIT_CARD_NO", searchCriteria.getCreditCardNo(), "CREDIT_CARD_NO");

        // ---------- UNION BLOĞU BAŞLANGIÇ ----------

        sql.append("""
                UNION ALL
                SELECT DISTINCT
                    I.PRODUCT_CODE AS PRODUCT,
                    I.INSTITUTION_CODE AS INSTITUTION,
                    S.SUBSCRIBER_NO,
                    PB.SUBSCRIBER_NAME,
                    PB.BILL_NO,
                    PB.BILL_DUE_DATE,
                    PB.BILL_ISSUE_DATE,
                    PB.BILL_TERM,
                    PB.AMOUNT AS BILL_AMOUNT,
                    NULL AS BILL_PAYMENT_AMOUNT,
                    PB.CURRENCY,
                    PB.STATUS,
                    NULL AS PAYMENT_METHOD,
                    NULL AS CONTRACT_NO,
                    '925' AS BRANCH_CODE,
                    '602' AS CHANNEL_CODE,
                    'IN_PROCESS' AS PAYMENT_NOTIFICATION_STATUS,
                    PO.CUSTOMER_NO,
                    (SELECT ACCOUNT_NO
                     FROM BILL.PAYMENT_ORDER_DETAIL
                     WHERE ORDER_ID = PO.ID AND SEQUENCE_NO IN(0,1)
                     FETCH FIRST 1 ROW ONLY) AS ACCOUNT_NO,
                    (SELECT CARD_NO
                     FROM BILL.PAYMENT_ORDER_DETAIL
                     WHERE ORDER_ID = PO.ID AND SEQUENCE_NO = 0
                     FETCH FIRST 1 ROW ONLY) AS CREDIT_CARD_NO,
                    NULL AS PAYMENT_DATE,
                    NULL AS PAYMENT_TIME,
                    PB.ADDITIONAL_INFO_1,
                    PB.ADDITIONAL_INFO_2,
                    PB.ADDITIONAL_INFO_3,
                    PB.ADDITIONAL_INFO_4,
                    PB.ADDITIONAL_INFO_5,
                    PB.ADDITIONAL_INFO_6,
                    PB.ADDITIONAL_INFO_7,
                    PB.ADDITIONAL_INFO_8,
                    PB.ADDITIONAL_INFO_9,
                    PO.CREATE_DATE,
                    PO.ORDER_METHOD,
                    NULL AS RECEIPT_CODE
                FROM BILL.PAYABLE_BILL PB
                JOIN BILL.SUBSCRIBER S  ON PB.INSTITUTION_ID = S.INSTITUTION_ID
                                       AND PB.INSTITUTION_DEBT_TYPE_ID = S.INSTITUTION_DEBT_TYPE_ID
                                       AND PB.SUBSCRIBER_NO = S.SUBSCRIBER_NO
                JOIN BILL.PAYMENT_ORDER PO ON PO.SUBSCRIBER_ID = S.ID
                JOIN BILL.INSTITUTION I     ON PB.INSTITUTION_ID = I.ID
                WHERE PO.STATUS = 'ORDERED'
                  AND PB.STATUS = 'NOT_PAID'
                  AND NOT EXISTS (
                      SELECT 1 FROM BILL.PAYMENT P
                      WHERE P.INSTITUTION_ID = PB.INSTITUTION_ID
                        AND P.INSTITUTION_DEBT_TYPE_ID = PB.INSTITUTION_DEBT_TYPE_ID
                        AND P.SUBSCRIBER_NO = PB.SUBSCRIBER_NO
                        AND P.BILL_NO = PB.BILL_NO
                        AND P.CUSTOMER_NO = PO.CUSTOMER_NO
                  )
                """);

        // ------ FİLTRELER (2. BÖLÜM) ------

        addCondition(sql, "PO.CUSTOMER_NO", searchCriteria.getCustomerNo(), "CUSTOMER_NO");
        addCondition(sql, "I.PRODUCT_CODE", searchCriteria.getProductCode(), "PRODUCT_CODE");
        addCondition(sql, "I.INSTITUTION_CODE", searchCriteria.getInstitutionCode(), "INSTITUTION_CODE");
        addCondition(sql, "PB.STATUS", searchCriteria.getStatus(), "STATUS");
        addCondition(sql, "PO.ORDER_METHOD", searchCriteria.getOrderMethod(), "ORDER_METHOD");

        if (searchCriteria.getQueryStartDate() != null && searchCriteria.getQueryEndDate() != null) {
            sql.append(" AND PB.BILL_DUE_DATE BETWEEN :QUERY_START_DATE AND :QUERY_END_DATE\n");
        }

        if (StringUtils.isNotEmpty(searchCriteria.getAccountNo())) {
            sql.append("""
                    AND :ACCOUNT_NO IN (
                        SELECT ACCOUNT_NO FROM BILL.PAYMENT_ORDER_DETAIL POD
                        WHERE POD.ORDER_ID = PO.ID AND POD.SEQUENCE_NO IN (0,1)
                    )
                    AND :ACCOUNT_NO IN (
                        SELECT ACCOUNT_NO FROM BILL.GENERAL_ACCOUNT GA
                        WHERE GA.CUSTOMER_NO = PO.CUSTOMER_NO AND GA.SEQUENCE_NO = 1
                    )
                    """);
        }

        addCondition(sql, "POD.CREDIT_CARD_NO", searchCriteria.getCreditCardNo(), "CREDIT_CARD_NO");

        return sql.toString();
    }

    // --- YARDIMCI METHOD ---
    private static void addCondition(StringBuilder sql, String column, Object value, String paramName) {
        if (value != null && !(value instanceof String s && s.isEmpty())) {
            sql.append(" AND ").append(column).append(" = :").append(paramName).append("\n");
        }
    }

    // --- PARAMETRE HAZIRLAMA ---
    public static MapSqlParameterSource prepareQueryParameters(FetchBaasOrderedDebtsSearchCriteria searchCriteria) {

        MapSqlParameterSource parameters = new MapSqlParameterSource();

        if (searchCriteria.getCustomerNo() != null) {
            parameters.addValue("CUSTOMER_NO", searchCriteria.getCustomerNo());
        }
        if (StringUtils.isNotEmpty(searchCriteria.getProductCode())) {
            parameters.addValue("PRODUCT_CODE", searchCriteria.getProductCode());
        }
        if (StringUtils.isNotEmpty(searchCriteria.getInstitutionCode())) {
            parameters.addValue("INSTITUTION_CODE", searchCriteria.getInstitutionCode());
        }
        if (StringUtils.isNotEmpty(searchCriteria.getStatus())) {
            parameters.addValue("STATUS", searchCriteria.getStatus());
        }
        if (StringUtils.isNotEmpty(searchCriteria.getOrderMethod())) {
            parameters.addValue("ORDER_METHOD", searchCriteria.getOrderMethod());
        }
        if (searchCriteria.getQueryStartDate() != null && searchCriteria.getQueryEndDate() != null) {
            parameters.addValue("QUERY_START_DATE", searchCriteria.getQueryStartDate());
            parameters.addValue("QUERY_END_DATE", searchCriteria.getQueryEndDate());
        }
        if (StringUtils.isNotEmpty(searchCriteria.getAccountNo())) {
            parameters.addValue("ACCOUNT_NO", searchCriteria.getAccountNo());
        }
        if (StringUtils.isNotEmpty(searchCriteria.getCreditCardNo())) {
            parameters.addValue("CREDIT_CARD_NO", searchCriteria.getCreditCardNo());
        }

        return parameters;
    }
}
