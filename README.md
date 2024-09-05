SELECT rm.*, rmd.*
FROM bill.return_map rm
JOIN bill.return_map_definition rmd 
ON rm.return_map_definition_id = rmd.id
WHERE rmd.return_map_code = 'BELEDİYE_NEVŞEHİR_QUERY_SUBSCRIBER';
