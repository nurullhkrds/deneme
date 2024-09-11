UPDATE OTOLIVE.v_t_oto_abone 
SET sablontipi = 'L'
WHERE musterino = '4740' 
AND statu = 'A'
AND urun NOT IN ('HAVALE', 'SGK', 'DBS', 'SİGORTA');
