SELECT
    s.urun,
    s.kurum
FROM otolive.t_oto_kurum s
WHERE
    /* Online process var mı */
    EXISTS (
        SELECT 1
        FROM otolive.pym_process_instances p
        WHERE p.product     = s.urun
          AND p.institution = s.kurum
          AND p.online_process = 'Y'
    )
    /* En az 1 aktif kanal var mı */
    AND EXISTS (
        SELECT 1
        FROM otolive.pym_process_channels c
        WHERE c.product     = s.urun
          AND c.institution = s.kurum
          AND c.aktif = 'A'     -- <-- aktif kanal kriteri (gerekirse değiştir)
          -- örn: AND c.status = 'ACTIVE'
          -- örn: AND c.end_date IS NULL
    )
    /* OTO MUTABAKAT'ta OLMAYANLAR */
    AND NOT EXISTS (
        SELECT 1
        FROM otolive.t_oto_mutabakat m
        WHERE m.urun  = s.urun
          AND m.kurum = s.kurum
    );



SELECT
    s.urun,
    COUNT(*) AS mutabakat_yok_kurum_sayisi
FROM otolive.t_oto_kurum s
WHERE
    EXISTS (
        SELECT 1
        FROM otolive.pym_process_instances p
        WHERE p.product     = s.urun
          AND p.institution = s.kurum
          AND p.online_process = 'Y'
    )
    AND EXISTS (
        SELECT 1
        FROM otolive.pym_process_channels c
        WHERE c.product     = s.urun
          AND c.institution = s.kurum
          AND c.aktif = 'A'
    )
    AND NOT EXISTS (
        SELECT 1
        FROM otolive.t_oto_mutabakat m
        WHERE m.urun  = s.urun
          AND m.kurum = s.kurum
    )
GROUP BY s.urun;

    
