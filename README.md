SELECT
    a.CUSTOMER_NO,
    a.NAME_SURNAME,
    a.PRODUCT,
    a.INSTITUTION,
    a.SUBSCRIBER_NO,
    a.CURRENCY_CODE,
    a.BILL_MONTH,          -- YYYYMM
    a.LAST_PAYMENT_DATE,   -- o ay içindeki son due date
    a.PAID_AMOUNT,
    a.PAID_COUNT,
    a.ACCOUNTING_CONTRACT_NO,
    a.TRANSACTION_CODE
FROM (
    SELECT
        -- Müşteri adı soyadı (hangi tablodan tuttuğunuza göre değiştir)
        -- örneğin PAYMENT içinde CUSTOMER_NAME varsa:
        MAX(P.CUSTOMER_NAME)                  AS NAME_SURNAME,

        P.CUSTOMER_NO                         AS CUSTOMER_NO,
        I.PRODUCT_CODE                        AS PRODUCT,
        I.INSTITUTION_CODE                    AS INSTITUTION,
        S.SUBSCRIBER_NO                       AS SUBSCRIBER_NO,
        P.CURRENCY                            AS CURRENCY_CODE,

        TO_CHAR(P.BILL_DUE_DATE,'YYYYMM')     AS BILL_MONTH,

        -- O ay içindeki son due date
        MAX(P.BILL_DUE_DATE)                  AS LAST_PAYMENT_DATE,

        -- O ayki toplam ödenen tutar ve ödeme adedi
        SUM(P.PAYMENT_AMOUNT)                 AS PAID_AMOUNT,
        COUNT(*)                              AS PAID_COUNT,

        -- Eski referans / dekonttip karşılığı olabilecek alanlar
        MAX(POR.ACCOUNTING_CONTRACT_NO)       AS ACCOUNTING_CONTRACT_NO,
        MAX(P.TRANSACTION_CODE)               AS TRANSACTION_CODE,

        ROW_NUMBER() OVER (
            PARTITION BY
                P.CUSTOMER_NO,
                I.PRODUCT_CODE,
                I.INSTITUTION_CODE,
                S.SUBSCRIBER_NO,
                P.CURRENCY,
                TO_CHAR(P.BILL_DUE_DATE,'YYYYMM')
            ORDER BY
                MAX(P.BILL_DUE_DATE) DESC
        ) AS RN
    FROM BILL.PAYMENT P
    JOIN BILL.INSTITUTION I
      ON P.INSTITUTION_ID = I.ID
    JOIN BILL.SUBSCRIBER S
      ON S.INSTITUTION_ID = I.ID
     AND S.INSTITUTION_ID = P.INSTITUTION_ID
     AND S.INSTITUTION_DEBT_TYPE_ID = P.INSTITUTION_DEBT_TYPE_ID
     AND S.SUBSCRIBER_NO = P.SUBSCRIBER_NO
    JOIN BILL.PAYMENT_ORDER POR
      ON POR.SUBSCRIBER_ID = S.ID
     AND POR.CUSTOMER_NO   = P.CUSTOMER_NO
    WHERE P.STATUS      = 'PAID'
      AND POR.STATUS    = 'ORDERED'
      AND P.CHANNEL_CODE = '602'
      AND P.BILL_DUE_DATE >= ADD_MONTHS(TRUNC(SYSDATE, 'MM'), -5)
      AND POR.CUSTOMER_NO = :CUSTOMER_NO  -- veya başka parametrelerin
    GROUP BY
        P.CUSTOMER_NO,
        I.PRODUCT_CODE,
        I.INSTITUTION_CODE,
        S.SUBSCRIBER_NO,
        P.CURRENCY,
        TO_CHAR(P.BILL_DUE_DATE,'YYYYMM')
) a
WHERE a.RN = 1
ORDER BY
    a.SUBSCRIBER_NO,
    a.BILL_MONTH;
