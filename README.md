SELECT
    s.urun,
    s.kurum
FROM otolive.t_oto_kurum s
WHERE EXISTS (
    SELECT 1
    FROM otolive.pym_process_instances p
    WHERE p.product     = s.urun
      AND p.institution = s.kurum
      AND p.online_process = 'Y'
)
AND NOT EXISTS (
    SELECT 1
    FROM otolive.t_oto_mutabakat m
    WHERE m.urun  = s.urun
      AND m.kurum = s.kurum
      -- eğer mutabakat tablosunda aktif/pasif alanı varsa:
      -- AND m.aktif = 'A'
);


SELECT
    s.urun,
    COUNT(*) AS mutabakat_yok_kurum_sayisi
FROM otolive.t_oto_kurum s
WHERE EXISTS (
    SELECT 1
    FROM otolive.pym_process_instances p
    WHERE p.product     = s.urun
      AND p.institution = s.kurum
      AND p.online_process = 'Y'
)
AND NOT EXISTS (
    SELECT 1
    FROM otolive.t_oto_mutabakat m
    WHERE m.urun  = s.urun
      AND m.kurum = s.kurum
)
GROUP BY s.urun;
