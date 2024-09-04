SELECT * FROM OTOLIVE.v_t_oto_abone s WHERE musterino='3693' and statu='A'
and urun not in ('HAVALE','SGK','DBS','SİGORTA') and (s.aboneno,s.urun,s.kurum) IN
(select a.aboneno,a.urun,a.kurum from otolive.sablon a where  cifno = 3693
       and hesapno ='00169935');


select hesapno,a.sirano,count(*) from otolive.sablon a where (a.aboneno,a.urun,a.kurum,a.cifno) IN (
SELECT s.aboneno,s.urun,s.kurum,s.musterino FROM OTOLIVE.v_t_oto_abone s WHERE musterino='3693' and statu='A'
and urun not in ('HAVALE','SGK','DBS','SİGORTA')) group by hesapno,sirano;


4)	Ilgili kriterlere uygun olan tüm kayıtların ürün, kurum bilgisi asagıdaki sql ile cekilir.

SELECT urun,kurum,count(*) FROM OTOLIVE.v_t_oto_abone WHERE musterino='3693' and statu='A'
and urun not in ('HAVALE','SGK','DBS','SİGORTA') group by urun,kurum;

İlgili ürün-kurum için x- y odmtipli kayıt var mı diye kontrol ediliyor. Özellikle y turunun kesinlikle tanımlı olması gerekiyor. Çünkü sabah turuna istinaden çalışan ve bakiye yetersiz( hots süreçleri sebebiyle) hatası alan kayıtları en azından ikinci kart turuna dahil edebilmek icin y turunun tanımlı olması gerekir.


select urun,kurum from otolive.t_oto_kurumdetay where (urun,kurum) IN (
SELECT urun,kurum FROM OTOLIVE.v_t_oto_abone WHERE musterino='3693' and statu='A'
and urun not in ('HAVALE','SGK','DBS','SİGORTA') group by urun,kurum) and odmtip ='X' and aktif='A';



select urun,kurum from otolive.t_oto_kurumdetay where (urun,kurum) IN (
SELECT urun,kurum FROM OTOLIVE.v_t_oto_abone WHERE musterino='3693' and statu='A'
and urun not in ('HAVALE','SGK','DBS','SİGORTA') group by urun,kurum) and odmtip ='Y' and aktif='A';

