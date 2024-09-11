SELECT * FROM OTOLIVE.v_t_oto_abone s WHERE musterino='3693' and statu='A'
and urun not in ('HAVALE','SGK','DBS','SİGORTA') and (s.aboneno,s.urun,s.kurum) IN
(select * from otolive.sablon a where  cifno = 3693
       and hesapno ='00169935');



