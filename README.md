SELECT a.*,
       (select count(*)
          from otolive.t_oto_fatura f, t_oto_abone abn
         where abn.Musterino = :CUSTOMER_NO
           and abn.statu = 'A'
           and f.urun = abn.urun
           and f.kurum = abn.kurum
           and f.aboneno = abn.aboneno
           and f.Musterino = abn.Musterino
           and f.statu = 'X'
           and f.Sonodmtarih between trunc(sysdate)-30 and trunc(sysdate)
           and EXISTS (SELECT 1
                  FROM OTOLIVE.T_OTO_KURUMDETAY det
                 where det.Urun = abn.Urun
                   and det.Kurum = abn.Kurum
                   and det.Aktif = 'A'
                   and det.ODMTIP in ('X',
                                      'Y',
                                      '0',
                                      '1',
                                      '2',
                                      '3',
                                      '4',
                                      '5',
                                      '6',
                                      '7',
                                      '8',
                                      '9'))
           and exists (select 1
                  from OTOLIVE.T_OTO_KURUM kur
                 where kur.Urun = abn.Urun
                   and kur.Kurum = abn.Kurum
                   and kur.kurumtip IN ('F', 'N', 'S'))
       @dynamic[DYNAMICLIST_FLAG, AND (TO_CHAR(f.Urun || '#' || f.Kurum || '#' || f.Aboneno)) in
           (SELECT *
              FROM table(sys.odcivarchar2list(''))]
           @dynamic[BILL_INFOLIST, union
           SELECT *
              FROM table(sys.odcivarchar2list(:BILL_INFOLIST))]
           @dynamic[INSTRUCTION_ID, union
            SELECT TO_CHAR(abn.Urun || '#' || abn.Kurum || '#' || abn.Aboneno) COLUMN_VALUE
              FROM otolive.t_oto_Abone abn
             where abn.ID in (:INSTRUCTION_ID)] @dynamic[DYNAMICLIST_FLAG, )]) COUNT_OF_NOTPAID_BILLS
     FROM (select case
                 when t.urun = 'ELEKTRİK' then
                  'ELEKTRİK'
                 when t.urun = 'DOĞALGAZ' then
                  'DOĞALGAZ'
                 when t.urun = 'CEPTEL' then
                  'CEPTEL'
                 when t.urun = 'SU' then
                  'SU'
                 when t.urun = 'TELEKOM' then
                  'TELEKOM'
                 ELSE
                  'DİĞER'
               END AS PRODUCT,
               NVL(sum(t.odntutar),0) AMOUNT_BASED_PRODUCT
          from otolive.t_oto_fatura t
         where t.musterino = :CUSTOMER_NO
           and t.statu IN ('P','X')
           and t.doviz IN ('TRY', 'TL', 'YTL')
           and (to_char(t.odmtarih, 'YYYYMM') =
               TO_CHAR(ADD_MONTHS(SYSDATE, 0), 'YYYYMM') or t.odmtarih is null)
           and exists (select 1
                  from OTOLIVE.T_OTO_KURUM kur
                 where kur.Urun = t.Urun
                   and kur.Kurum = t.Kurum
                   and kur.kurumtip IN ('F', 'N', 'S'))
           @dynamic[DYNAMICLIST_FLAG, AND (TO_CHAR(t.Urun || '#' || t.Kurum || '#' || t.Aboneno)) in
           (SELECT *
              FROM table(sys.odcivarchar2list(''))]
         @dynamic[BILL_INFOLIST, union
           SELECT *
              FROM table(sys.odcivarchar2list(:BILL_INFOLIST))]
           @dynamic[INSTRUCTION_ID, union
            SELECT TO_CHAR(abn.Urun || '#' || abn.Kurum || '#' || abn.Aboneno) COLUMN_VALUE
              FROM otolive.t_oto_Abone abn
             where abn.ID in (:INSTRUCTION_ID)] @dynamic[DYNAMICLIST_FLAG, )]
         group by case
                    when t.urun = 'ELEKTRİK' then
                     'ELEKTRİK'
                    when t.urun = 'DOĞALGAZ' then
                     'DOĞALGAZ'
                    when t.urun = 'CEPTEL' then
                     'CEPTEL'
                    when t.urun = 'SU' then
                     'SU'
                    when t.urun = 'TELEKOM' then
                     'TELEKOM'
                    ELSE
                     'DİĞER'
                  END) a








SELECT A.*,
       (
         SELECT COUNT(*)
         FROM BILL.PAYABLE_BILL PB
         JOIN BILL.SUBSCRIBER S
           ON PB.INSTITUTION_ID = S.INSTITUTION_ID
          AND PB.INSTITUTION_DEBT_TYPE_ID = S.INSTITUTION_DEBT_TYPE_ID
          AND PB.SUBSCRIBER_NO = S.SUBSCRIBER_NO
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
           AND TO_CHAR(I.PRODUCT_CODE || '#' || I.INSTITUTION_CODE || '#' || S.SUBSCRIBER_NO)
               IN (
                   SELECT *
                   FROM TABLE(SYS.ODCIVARCHAR2LIST(''))
                   UNION
                   SELECT TO_CHAR(I2.PRODUCT_CODE || '#' || I2.INSTITUTION_CODE || '#' || S2.SUBSCRIBER_NO)
                   FROM BILL.PAYMENT_ORDER POR2
                   JOIN BILL.SUBSCRIBER S2 ON POR2.SUBSCRIBER_ID = S2.ID
                   JOIN BILL.INSTITUTION I2 ON I2.ID = S2.INSTITUTION_ID
                   WHERE POR2.ID IN (12321)
               )
       ) AS COUNT_OF_NOTPAID_BILLS
FROM (
      SELECT
         CASE
           WHEN PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK'
           WHEN PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ'
           WHEN PRODUCT_CODE = 'CEPTEL'   THEN 'CEPTEL'
           WHEN PRODUCT_CODE = 'SU'       THEN 'SU'
           WHEN PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM'
           ELSE 'DİĞER'
         END AS PRODUCT,
         NVL(SUM(AMOUNT), 0) AS PRODUCT_BASE_AMOUNT
      FROM (
            SELECT 
              P2.PAYMENT_AMOUNT AS AMOUNT,
              I3.PRODUCT_CODE AS PRODUCT_CODE,
              TO_CHAR(I3.PRODUCT_CODE || '#' || I3.INSTITUTION_CODE || '#' || S2.SUBSCRIBER_NO) AS INFO_KEY
            FROM BILL.PAYMENT P2
            JOIN BILL.INSTITUTION I3 ON P2.INSTITUTION_ID = I3.ID
            JOIN BILL.SUBSCRIBER S2 ON S2.INSTITUTION_ID = I3.ID
            WHERE P2.CUSTOMER_NO = 3015
              AND P2.STATUS = 'PAID'
              AND P2.CURRENCY = 'YTL'
              AND TO_CHAR(P2.PAYMENT_DATE, 'YYYYMM') = TO_CHAR(SYSDATE, 'YYYYMM')
              AND I3.PRODUCT_CODE NOT IN ('BELEDİYE')
 
            UNION ALL
 
            SELECT 
              PB2.AMOUNT AS AMOUNT,
              I3.PRODUCT_CODE AS PRODUCT_CODE,
              TO_CHAR(I3.PRODUCT_CODE || '#' || I3.INSTITUTION_CODE || '#' || S3.SUBSCRIBER_NO) AS INFO_KEY
            FROM BILL.PAYABLE_BILL PB2
            JOIN BILL.INSTITUTION I3 ON I3.ID = PB2.INSTITUTION_ID
            JOIN BILL.SUBSCRIBER S3
              ON PB2.INSTITUTION_ID = S3.INSTITUTION_ID
             AND PB2.INSTITUTION_DEBT_TYPE_ID = S3.INSTITUTION_DEBT_TYPE_ID
             AND PB2.SUBSCRIBER_NO = S3.SUBSCRIBER_NO
            JOIN BILL.PAYMENT_ORDER PO2
              ON PO2.SUBSCRIBER_ID = S3.ID
             AND PO2.CUSTOMER_NO = 3015
            WHERE PB2.STATUS = 'OVERDUE'
              AND PO2.STATUS = 'ORDERED'
         ) PAID_AND_OVERDUE_BILLS_AMOUNTS
      WHERE INFO_KEY IN (
              SELECT *
              FROM TABLE(SYS.ODCIVARCHAR2LIST(''))
              UNION
              SELECT TO_CHAR(I4.PRODUCT_CODE || '#' || I4.INSTITUTION_CODE || '#' || S4.SUBSCRIBER_NO)
              FROM BILL.PAYMENT_ORDER POR3
              JOIN BILL.SUBSCRIBER S4 ON POR3.SUBSCRIBER_ID = S4.ID
              JOIN BILL.INSTITUTION I4 ON I4.ID = S4.INSTITUTION_ID
              WHERE POR3.ID IN (213213)
          )
      GROUP BY 
         CASE
           WHEN PRODUCT_CODE = 'ELEKTRİK' THEN 'ELEKTRİK'
           WHEN PRODUCT_CODE = 'DOĞALGAZ' THEN 'DOĞALGAZ'
           WHEN PRODUCT_CODE = 'CEPTEL'   THEN 'CEPTEL'
           WHEN PRODUCT_CODE = 'SU'       THEN 'SU'
           WHEN PRODUCT_CODE = 'TELEKOM'  THEN 'TELEKOM'
           ELSE 'DİĞER'
         END
) A;






                  
