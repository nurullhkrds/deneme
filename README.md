SELECT a.NameSurname,
       a.Product,
       a.Institution,
       a.SubscriberNo,
       a.CurrencyCode,
       a.LastPaymentDate,
       a.PaidAmount,
       a.PaidCount,
       a.NotPaidAmount,
       a.NotPaidCount,
       a.PayableAmount,
       a.PayableCount,
       a.AccountingContractNumber,
       a.TransactionCode
  FROM (SELECT fat.Adsoyad NameSurname,
               fat.Urun Product,
               fat.Kurum Institution,
               fat.Aboneno SubscriberNo,
               DECODE(fat.Doviz, 'YTL', 'TL', fat.Doviz) CurrencyCode,
               fat.Sonodmtarih LastPaymentDate,
               (SELECT SUM(fat2.odntutar) tutar
                  FROM OTOLIVE.T_OTO_FATURA fat2
                 where fat2.Urun = fat.Urun
                   and fat2.Kurum = fat.Kurum
                   and fat2.Aboneno = fat.Aboneno
                   and fat2.Musterino = fat.Musterino
                   and to_char(fat2.Sonodmtarih, 'YYYYMM') =
                       to_char(fat.Sonodmtarih, 'YYYYMM')
                   and fat2.Statu = 'P'
                   and fat2.odmtip IN ('O', 'K')
                 group by fat2.Urun,
                          fat2.Kurum,
                          fat2.Aboneno,
                          to_char(fat2.Sonodmtarih, 'YYYYMM')) PaidAmount,(SELECT COUNT(*)
                  FROM OTOLIVE.T_OTO_FATURA fat2
                 where fat2.Urun = fat.Urun
                   and fat2.Kurum = fat.Kurum
                   and fat2.Aboneno = fat.Aboneno
                   and fat2.Musterino = fat.Musterino
                   and to_char(fat2.Sonodmtarih, 'YYYYMM') =
                       to_char(fat.Sonodmtarih, 'YYYYMM')
                   and fat2.Statu = 'P'
                   and fat2.odmtip IN ('O', 'K')
                 group by fat2.Urun,
                          fat2.Kurum,
                          fat2.Aboneno,
                          to_char(fat2.Sonodmtarih, 'YYYYMM')) PaidCount,
               (SELECT SUM(fat3.tutar) tutar
                  FROM OTOLIVE.T_OTO_FATURA fat3
                 where fat3.Urun = fat.Urun
                   and fat3.Kurum = fat.Kurum
                   and fat3.Aboneno = fat.Aboneno
                   and fat3.Musterino = fat.Musterino
                   and to_char(fat3.Sonodmtarih, 'YYYYMM') =
                       to_char(fat.Sonodmtarih, 'YYYYMM')
                   and to_char(fat3.Sonodmtarih, 'YYYYMM') >=
                       TO_CHAR(ADD_MONTHS(SYSDATE, -3), 'YYYYMM')
                   and fat3.Statu in ('X')
                 group by fat3.Urun,
                          fat3.Kurum,
                          fat3.Aboneno,
                          to_char(fat3.Sonodmtarih, 'YYYYMM')) NotPaidAmount,(SELECT TO_CHAR(COUNT(*)) || '#' ||
                       LISTAGG(TO_CHAR(fat3.Sonodmtarih, 'dd/MM/yyyy'), ',') WITHIN GROUP(order by fat3.Sonodmtarih) as tarihler
                  FROM OTOLIVE.T_OTO_FATURA fat3
                 where fat3.Urun = fat.Urun
                   and fat3.Kurum = fat.Kurum
                   and fat3.Aboneno = fat.Aboneno
                   and fat3.Musterino = fat.Musterino
                   and to_char(fat3.Sonodmtarih, 'YYYYMM') =
                       to_char(fat.Sonodmtarih, 'YYYYMM')
                   and to_char(fat3.Sonodmtarih, 'YYYYMM') >=
                       TO_CHAR(ADD_MONTHS(SYSDATE, -3), 'YYYYMM')
                   and fat3.Statu = 'X'
                   and EXISTS (SELECT 1
                          FROM OTOLIVE.T_OTO_KURUMDETAY det
                         where det.Urun = fat3.Urun
                           and det.Kurum = fat3.Kurum
                           and det.Aktif = 'A'
               and det.Odmtip in ('X','Y','0','1','2','3','4','5','6','7','8','9'))
                   and exists
                 (select 1
                          from OTOLIVE.T_OTO_KURUM kur
                         where kur.Urun = fat3.Urun
                           and kur.Kurum = fat3.Kurum
                           and kur.kurumtip IN ('F', 'N', 'S'))
                 group by fat3.Urun,
                          fat3.Kurum,
                          fat3.Aboneno,
                          to_char(fat3.Sonodmtarih, 'YYYYMM')) NotPaidCount,(SELECT SUM(fat3.tutar) tutar
                  FROM OTOLIVE.T_OTO_FATURA fat3
                 where fat3.Urun = fat.Urun
                   and fat3.Kurum = fat.Kurum
                   and fat3.Aboneno = fat.Aboneno
                   and fat3.Musterino = fat.Musterino
                   and to_char(fat3.Sonodmtarih, 'YYYYMM') =
                       to_char(fat.Sonodmtarih, 'YYYYMM')
                   and fat3.sonodmtarih >= trunc(sysdate)
                   and fat3.Statu = 'N'
                 group by fat3.Urun,
                          fat3.Kurum,
                          fat3.Aboneno,
                          to_char(fat3.Sonodmtarih, 'YYYYMM')) PayableAmount,(SELECT TO_CHAR(COUNT(*)) || '#' ||
                       LISTAGG(TO_CHAR(fat3.Sonodmtarih, 'dd/MM/yyyy'), ',') WITHIN GROUP(order by fat3.Sonodmtarih) as tarihler
                  FROM OTOLIVE.T_OTO_FATURA fat3
                 where fat3.Urun = fat.Urun
                   and fat3.Kurum = fat.Kurum
                   and fat3.Aboneno = fat.Aboneno
                   and fat3.Musterino = fat.Musterino
                   and to_char(fat3.Sonodmtarih, 'YYYYMM') =
                       to_char(fat.Sonodmtarih, 'YYYYMM')
                   and fat3.sonodmtarih >= trunc(sysdate)
                   and fat3.Statu = 'N'
                   and exists (select 1
                          from otolive.t_oto_abone abn
                         where abn.Urun = fat3.Urun
                           and abn.Kurum = fat3.Kurum
                           and abn.Aboneno = fat3.Aboneno
                           and abn.MusteriNo = fat3.Musterino
                           and abn.sablontipi IN ('L', 'K'))
                   and EXISTS (SELECT 1
                          FROM OTOLIVE.T_OTO_KURUMDETAY det
                         where det.Urun = fat3.Urun
                           and det.Kurum = fat3.Kurum
                           and det.Aktif = 'A'
               and det.Odmtip in ('X','Y','0','1','2','3','4','5','6','7','8','9'))
                   and exists
                 (select 1
                          from OTOLIVE.T_OTO_KURUM kur
                         where kur.Urun = fat3.Urun
                           and kur.Kurum = fat3.Kurum
                           and kur.kurumtip IN ('F', 'N', 'S'))
                 group by fat3.Urun,
                          fat3.Kurum,
                          fat3.Aboneno,
                          to_char(fat3.Sonodmtarih, 'YYYYMM')) PayableCount, fat.referans AccountingContractNumber,
               kur.Dekonttip TransactionCode,
               ROW_NUMBER() OVER(PARTITION BY fat.Aboneno, to_char(fat.Sonodmtarih, 'YYYYMM') order by to_char(fat.Sonodmtarih, 'YYYYMM')) row_number
          FROM OTOLIVE.T_OTO_FATURA fat, OTOLIVE.T_OTO_KURUM kur
         WHERE fat.Urun = :PRODUCT
           and fat.Kurum = :INSTITUTION
           and fat.Aboneno = :SUBSCRIBER_NO
           and fat.Musterino = :CUSTOMER_NO
           and kur.Urun = fat.Urun
           and kur.Kurum = fat.Kurum
           and TO_CHAR(fat.Sonodmtarih, 'YYYYMM') >
               TO_CHAR(ADD_MONTHS(SYSDATE, -5), 'YYYYMM')
         order by to_char(fat.Sonodmtarih, 'YYYYMM')) a
 where a.row_number = 1











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
     AND POR.CUSTOMER_NO = P.CUSTOMER_NO
   WHERE P.STATUS = 'PAID'
     AND P.BILL_DUE_DATE >= ADD_MONTHS(TRUNC(SYSDATE, 'MM'), -5)
     AND POR.STATUS = 'ORDERED'
     AND P.CHANNEL_CODE = '602'
