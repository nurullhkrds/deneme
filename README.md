UPDATE bill.service s
SET s.return_map_code = (
  SELECT rm.return_map_code
  FROM bill.return_map rm
  WHERE
    TRANSLATE(REPLACE(UPPER(s.name), 'BELEDIYE', 'BLD'), 'ÇĞİÖŞÜ', 'CGIOSU')
      LIKE '%' || TRANSLATE(REPLACE(UPPER(rm.return_map_code), 'BELEDIYE', 'BLD'), 'ÇĞİÖŞÜ', 'CGIOSU') || '%'
  FETCH FIRST 1 ROW ONLY
)
WHERE s.return_map_code IS NULL;



SELECT s.id, s.name, rm.return_map_code
FROM bill.service s
JOIN bill.return_map rm
  ON TRANSLATE(REPLACE(UPPER(s.name), 'BELEDIYE', 'BLD'), 'ÇĞİÖŞÜ', 'CGIOSU')
     LIKE '%' || TRANSLATE(REPLACE(UPPER(rm.return_map_code), 'BELEDIYE', 'BLD'), 'ÇĞİÖŞÜ', 'CGIOSU') || '%'
WHERE s.return_map_code IS NULL;
