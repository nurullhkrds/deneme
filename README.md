WITH KEY_LIST AS (
    -- 1) Dışarıdan gelen dinamik liste
    SELECT COLUMN_VALUE AS INFO_KEY
      FROM TABLE(SYS.ODCIVARCHAR2LIST(:DYNAMIC_LIST))

    UNION

    -- 2) BILL_INFOLIST parametresinden gelen key'ler
    SELECT COLUMN_VALUE AS INFO_KEY
      FROM TABLE(SYS.ODCIVARCHAR2LIST(:BILL_INFOLIST))

    UNION

    -- 3) INSTRUCTION_ID'lerden üretilen key'ler
    SELECT TO_CHAR(I.PRODUCT_CODE || '#' || I.INSTITUTION_CODE || '#' || S.SUBSCRIBER_NO) AS INFO_KEY
      FROM BILL.PAYMENT_ORDER PO
      JOIN BILL.SUBSCRIBER    S ON PO.SUBSCRIBER_ID = S.ID
      JOIN BILL.INSTITUTION   I ON I.ID             = S.INSTITUTION_ID
     WHERE PO.ID IN (:INSTRUCTION_ID)
),

PAID_AND_OVERDUE AS (
    ----------------------------------------------------------------
    -- Müşterinin ödenmiş (PAID) ve siparişi verilmiş gecikmiş (OVERDUE)
    -- faturalarını tek yerde topluyoruz.
    ----------------------------------------------------------------
    SELECT 
        CASE
            WHEN I3.PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK'
            WHEN I3.PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ'
            WHEN I3.PRODUCT_CODE = 'CEPTEL'   THEN 'CEPTEL'
            WHEN I3.PRODUCT_CODE = 'SU'       THEN 'SU'
            WHEN I3.PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM'
            ELSE 'DİĞER'
        END AS PRODUCT_GROUP,
        P2.PAYMENT_AMOUNT AS AMOUNT,
        TO_CHAR(I3.PRODUCT_CODE || '#' || I3.INSTITUTION_CODE || '#' || P2.SUBSCRIBER_NO) AS INFO_KEY
    FROM BILL.PAYMENT       P2
    JOIN BILL.INSTITUTION   I3 ON P2.INSTITUTION_ID = I3.ID
   WHERE P2.CUSTOMER_NO = :CUSTOMER_NO
     AND P2.STATUS      = 'PAID'
     AND P2.CURRENCY    = 'YTL'
     AND TO_CHAR(P2.PAYMENT_DATE, 'YYYYMM') = TO_CHAR(SYSDATE, 'YYYYMM')
     AND I3.PRODUCT_CODE NOT IN ('BELEDİYE')

    UNION ALL

    SELECT
        CASE
            WHEN I3.PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK'
            WHEN I3.PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ'
            WHEN I3.PRODUCT_CODE = 'CEPTEL'   THEN 'CEPTEL'
            WHEN I3.PRODUCT_CODE = 'SU'       THEN 'SU'
            WHEN I3.PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM'
            ELSE 'DİĞER'
        END AS PRODUCT_GROUP,
        PB2.AMOUNT AS AMOUNT,
        TO_CHAR(I3.PRODUCT_CODE || '#' || I3.INSTITUTION_CODE || '#' || S3.SUBSCRIBER_NO) AS INFO_KEY
    FROM BILL.PAYABLE_BILL  PB2
    JOIN BILL.INSTITUTION   I3 ON I3.ID = PB2.INSTITUTION_ID
    JOIN BILL.SUBSCRIBER    S3
      ON PB2.INSTITUTION_ID          = S3.INSTITUTION_ID
     AND PB2.INSTITUTION_DEBT_TYPE_ID = S3.INSTITUTION_DEBT_TYPE_ID
     AND PB2.SUBSCRIBER_NO           = S3.SUBSCRIBER_NO
    JOIN BILL.PAYMENT_ORDER PO2
      ON PO2.SUBSCRIBER_ID = S3.ID
     AND PO2.CUSTOMER_NO   = :CUSTOMER_NO
   WHERE PB2.STATUS = 'OVERDUE'
     AND PO2.STATUS = 'ORDERED'
),

AGGREGATED_AMOUNTS AS (
    -------------------------------------------------------------
    -- Ürün grubuna (PRODUCT_GROUP) göre toplam tutarı hesaplıyoruz
    -- ve sadece KEY_LIST içinde olan INFO_KEY’leri alıyoruz.
    -------------------------------------------------------------
    SELECT
        PRODUCT_GROUP AS PRODUCT,
        NVL(SUM(AMOUNT), 0) AS PRODUCT_BASE_AMOUNT
    FROM PAID_AND_OVERDUE
   WHERE INFO_KEY IN (SELECT INFO_KEY FROM KEY_LIST)
   GROUP BY PRODUCT_GROUP
)

SELECT A.*,
       -----------------------------------------------------------------
       -- Son 30 gündeki, siparişi verilmiş ama ödenmemiş (OVERDUE)
       -- fatura sayısını hesaplıyoruz.
       -----------------------------------------------------------------
       (
         SELECT COUNT(*)
           FROM BILL.PAYABLE_BILL PB
           JOIN BILL.SUBSCRIBER   S
             ON PB.INSTITUTION_ID          = S.INSTITUTION_ID
            AND PB.INSTITUTION_DEBT_TYPE_ID = S.INSTITUTION_DEBT_TYPE_ID
            AND PB.SUBSCRIBER_NO           = S.SUBSCRIBER_NO
           JOIN BILL.PAYMENT_ORDER PO
             ON PO.SUBSCRIBER_ID = S.ID
           JOIN BILL.INSTITUTION I
             ON I.ID = PB.INSTITUTION_ID
            AND I.ID = S.INSTITUTION_ID
          WHERE PB.STATUS      = 'OVERDUE'
            AND PO.STATUS      = 'ORDERED'
            AND PB.BILL_DUE_DATE BETWEEN TRUNC(SYSDATE) - 30
                                     AND TRUNC(SYSDATE)
            AND EXISTS (
                   SELECT 1
                     FROM BILL.INSTITUTION_ORDER_PYM_METHOD IOPM
                    WHERE IOPM.INSTITUTION_ID = PB.INSTITUTION_ID
                      AND IOPM.ORDER_METHOD IN ('ACCOUNT_LIST',
                                                'CREDIT_CARD',
                                                'GENERAL_ACCOUNT')
                      AND IOPM.IS_ACTIVE = 1
                )
            AND TO_CHAR(I.PRODUCT_CODE || '#' || I.INSTITUTION_CODE || '#' || S.SUBSCRIBER_NO)
                IN (SELECT INFO_KEY FROM KEY_LIST)
       ) AS COUNT_OF_NOTPAID_BILLS
FROM AGGREGATED_AMOUNTS A;
