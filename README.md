WITH BASE AS (
    ------------------------------------------------------------------
    -- 1) ÖDENMİŞ FATURALAR (BILL.PAYMENT)
    ------------------------------------------------------------------
    SELECT
        P.CUSTOMER_NO                             AS CUSTOMER_NO,
        P.SUBSCRIBER_NAME                         AS NAME_SURNAME,
        I.PRODUCT_CODE                            AS PRODUCT,
        I.INSTITUTION_CODE                        AS INSTITUTION,
        S.SUBSCRIBER_NO                           AS SUBSCRIBER_NO,
        P.CURRENCY                                AS CURRENCY_CODE,
        TO_CHAR(P.BILL_DUE_DATE, 'YYYYMM')        AS BILL_MONTH,

        P.BILL_DUE_DATE                           AS BILL_DUE_DATE_PAID,
        P.CONTRACT_NO                             AS CONTRACT_NO_PAID,
        P.RECEIPT_CODE                            AS RECEIPT_CODE_PAID,

        P.PAYMENT_AMOUNT                          AS PAID_AMOUNT,
        1                                         AS PAID_COUNT,

        0                                         AS NOT_PAID_AMOUNT,
        0                                         AS NOT_PAID_COUNT,
        0                                         AS PAYABLE_AMOUNT,
        0                                         AS PAYABLE_COUNT
    FROM BILL.PAYMENT P
    JOIN BILL.INSTITUTION I
      ON P.INSTITUTION_ID = I.ID
    JOIN BILL.SUBSCRIBER S
      ON S.INSTITUTION_ID           = I.ID
     AND S.INSTITUTION_ID           = P.INSTITUTION_ID
     AND S.INSTITUTION_DEBT_TYPE_ID = P.INSTITUTION_DEBT_TYPE_ID
     AND S.SUBSCRIBER_NO            = P.SUBSCRIBER_NO
    JOIN BILL.PAYMENT_ORDER POR
      ON POR.SUBSCRIBER_ID = S.ID
     AND POR.CUSTOMER_NO   = P.CUSTOMER_NO
    WHERE P.STATUS        = 'PAID'
      AND POR.STATUS      = 'ORDERED'
      AND P.CHANNEL_CODE  = '602'
      AND P.BILL_DUE_DATE >= ADD_MONTHS(TRUNC(SYSDATE, 'MM'), -5)
      AND POR.CUSTOMER_NO = :CUSTOMER_NO

    UNION ALL

    ------------------------------------------------------------------
    -- 2) ÖDENMEMİŞ FATURALAR (BILL.PAYABLE_BILL)
    --    OVERDUE  -> NOT_PAID_AMOUNT / NOT_PAID_COUNT
    --    NOT_PAID -> PAYABLE_AMOUNT  / PAYABLE_COUNT
    ------------------------------------------------------------------
    SELECT
        POR.CUSTOMER_NO                           AS CUSTOMER_NO,
        NULL                                      AS NAME_SURNAME,
        I.PRODUCT_CODE                            AS PRODUCT,
        I.INSTITUTION_CODE                        AS INSTITUTION,
        S.SUBSCRIBER_NO                           AS SUBSCRIBER_NO,
        PB.CURRENCY                               AS CURRENCY_CODE,
        TO_CHAR(PB.BILL_DUE_DATE, 'YYYYMM')       AS BILL_MONTH,

        NULL                                      AS BILL_DUE_DATE_PAID,
        NULL                                      AS CONTRACT_NO_PAID,
        NULL                                      AS RECEIPT_CODE_PAID,

        0                                         AS PAID_AMOUNT,
        0                                         AS PAID_COUNT,

        CASE WHEN PB.STATUS = 'OVERDUE'  THEN PB.AMOUNT ELSE 0 END AS NOT_PAID_AMOUNT,
        CASE WHEN PB.STATUS = 'OVERDUE'  THEN 1          ELSE 0 END AS NOT_PAID_COUNT,
        CASE WHEN PB.STATUS = 'NOT_PAID' THEN PB.AMOUNT ELSE 0 END AS PAYABLE_AMOUNT,
        CASE WHEN PB.STATUS = 'NOT_PAID' THEN 1          ELSE 0 END AS PAYABLE_COUNT
    FROM BILL.PAYABLE_BILL PB
    JOIN BILL.INSTITUTION I
      ON PB.INSTITUTION_ID = I.ID
    JOIN BILL.SUBSCRIBER S
      ON S.INSTITUTION_ID           = I.ID
     AND S.INSTITUTION_ID           = PB.INSTITUTION_ID
     AND S.INSTITUTION_DEBT_TYPE_ID = PB.INSTITUTION_DEBT_TYPE_ID
     AND S.SUBSCRIBER_NO            = PB.SUBSCRIBER_NO
    JOIN BILL.PAYMENT_ORDER POR
      ON POR.SUBSCRIBER_ID = S.ID
     AND POR.CUSTOMER_NO   = PB.CUSTOMER_NO
    WHERE PB.STATUS       IN ('OVERDUE', 'NOT_PAID')
      AND POR.STATUS      = 'ORDERED'
      AND PB.BILL_DUE_DATE >= ADD_MONTHS(TRUNC(SYSDATE, 'MM'), -5)
      AND POR.CUSTOMER_NO = :CUSTOMER_NO
)
SELECT
    B.CUSTOMER_NO,
    MAX(B.NAME_SURNAME)                            AS NAME_SURNAME,
    B.PRODUCT,
    B.INSTITUTION,
    B.SUBSCRIBER_NO,
    B.CURRENCY_CODE,
    B.BILL_MONTH,

    MAX(B.BILL_DUE_DATE_PAID)                      AS LAST_PAYMENT_DATE,
    SUM(B.PAID_AMOUNT)                             AS PAID_AMOUNT,
    SUM(B.PAID_COUNT)                              AS PAID_COUNT,

    MAX(B.CONTRACT_NO_PAID) KEEP (
        DENSE_RANK LAST ORDER BY B.BILL_DUE_DATE_PAID
    )                                              AS ACCOUNTING_CONTRACT_NO,
    MAX(B.RECEIPT_CODE_PAID) KEEP (
        DENSE_RANK LAST ORDER BY B.BILL_DUE_DATE_PAID
    )                                              AS TRANSACTION_CODE,

    SUM(B.NOT_PAID_AMOUNT)                         AS NOT_PAID_AMOUNT,
    SUM(B.NOT_PAID_COUNT)                          AS NOT_PAID_COUNT,
    SUM(B.PAYABLE_AMOUNT)                          AS PAYABLE_AMOUNT,
    SUM(B.PAYABLE_COUNT)                           AS PAYABLE_COUNT
FROM BASE B
GROUP BY
    B.CUSTOMER_NO,
    B.PRODUCT,
    B.INSTITUTION,
    B.SUBSCRIBER_NO,
    B.CURRENCY_CODE,
    B.BILL_MONTH
ORDER BY
    B.SUBSCRIBER_NO,
    B.BILL_MONTH;
