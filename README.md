MERGE INTO bill.service s
USING (
  WITH src AS (
    SELECT q'[
'BELEDİYE_İSTANBUL_QUERY_BILLS', 'BLDISTANBUL_RETURNMAP_ALL');
'BELEDİYE_İSTANBUL_NOTIFY_PAYMENT', 'BLDISTANBUL_RETURNMAP_ALL');
'BELEDİYE_İSTANBUL_NOTIFY_PAYMENT_CANCEL', 'BLDISTANBUL_RETURNMAP_ALL');
-- >>> BURAYA SENİN TÜM LİSTEYİ AYNEN YAPIŞTIR (DEVAMINI) <<<
]' AS txt
    FROM dual
  ),
  toks AS (
    -- metindeki tüm '...' değerlerini tek tek çıkarır
    SELECT
      LEVEL AS rn,
      REGEXP_SUBSTR(txt, '''([^'']*)''', 1, LEVEL, 'n', 1) AS val
    FROM src
    CONNECT BY LEVEL <= REGEXP_COUNT(txt, '''[^'']*''')
  ),
  pairs AS (
    -- 1. tırnak = name, 2. tırnak = return_map_code şeklinde ikili yapar
    SELECT
      val AS service_name,
      LEAD(val) OVER (ORDER BY rn) AS new_return_map_code
    FROM toks
    WHERE MOD(rn, 2) = 1
  )
  SELECT DISTINCT service_name, new_return_map_code
  FROM pairs
  WHERE new_return_map_code IS NOT NULL
) m
ON (s.name = m.service_name)
WHEN MATCHED THEN
  UPDATE SET s.return_map_code = m.new_return_map_code;





WITH src AS (
  SELECT q'[
-- listeyi buraya aynen yapıştır
]' AS txt FROM dual
),
toks AS (
  SELECT LEVEL rn, REGEXP_SUBSTR(txt, '''([^'']*)''', 1, LEVEL, 'n', 1) val
  FROM src
  CONNECT BY LEVEL <= REGEXP_COUNT(txt, '''[^'']*''')
),
pairs AS (
  SELECT val service_name, LEAD(val) OVER (ORDER BY rn) new_return_map_code
  FROM toks
  WHERE MOD(rn,2)=1
)
SELECT s.id, s.name, s.return_map_code old_code, p.new_return_map_code new_code
FROM bill.service s
JOIN pairs p ON p.service_name = s.name;
  
