
![image](https://github.com/user-attachments/assets/a004ae9b-0e07-4587-a0ce-04a54152110c)

![image](https://github.com/user-attachments/assets/ebf62e4d-6762-4be2-b3f3-dd151b318e92)




![image](https://github.com/user-attachments/assets/b0d3823d-0439-4156-8186-ae39e66e263d)




PYM_ONLINE_SERVICE_LOG

SELECT /*+ PARALLEL(64) */
 MAX(LOG_TIME) LOGDATE, RESULT_CODE, SUBSCRIBER_NO
  FROM OTOLIVE.PYM_ONLINE_SERVICE_LOG
 WHERE INSTITUTION = 'İZMİRGAZ' -- Kurum Filtresi
   AND PRODUCT = 'DOĞALGAZ' -- Ürün Filtresi
   and SUBSCRIBER_NO IN ('3866030000',
'1562',
'1342',
'232',
'32132',
'12312312',
'123123'')  -- Abone No larının listesi Filtresi
   And process_code like '%NOTIFY%' -- Ödeme Bildirimi Filtresi
   AND LOG_DATE BETWEEN '21.08.2024' AND '23.08.2024' -- bildirim Tarih aralığı (Tek gün seçilecek) Filtresi
   AND ERROR != 00000  -- Başarılı tamamlanmamış olanlar (Sabit)
 GROUP BY RESULT_CODE, SUBSCRIBER_NO  -- Hata Kodu ve Abone No eşleşmesi
 ORDER BY SUBSCRIBER_NO, LOGDATE DESC

Yukarıdaki sorgudan abone no bazında dönen hata kodları kurum hata kodları eşleşme tablosundan mesajı çekilerek kullanıcıya gösterilir.

PYM_ONLINE_RETURN_MAP

select * from otolive.pym_online_return_map WHERE PRODUCT ='SU' AND INSTITUTION ='ASKI' AND INSTITUTION_RETURN_CODE ='02320'


birde select * from otolive.t_oto_fatura diye bir tablomuz var burdada SUBSCRIBER_NO=ABONENO PRODUCT=URUN,INSTITUTION=KURUM
