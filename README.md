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
