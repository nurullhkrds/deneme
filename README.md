SELECT rsl.*, rm.*
FROM RemoteServiceLog rsl
JOIN ReturnMap rm 
    ON rsl.INSTITUTION_RETURN_CODE = rm.INSTITUTION_RETURN_CODE
    AND rsl.BANK_RETURN_CODE = rm.BANK_RETURN_CODE
JOIN Institution inst
    ON rsl.INSTITUTION_ID = inst.ID
WHERE rsl.SERVICE_TYPE = 'NOTIFY_PAYMENT'
AND inst.INSTITUTION_CODE = :institutionCode
AND inst.PRODUCT_CODE = :product
AND rm.RETURN_MAP_CODE = :returnMapCode
AND rsl.LOG_DATE BETWEEN TO_DATE(:p_startDate, 'DD.MM.YYYY') AND TO_DATE(:p_endDate, 'DD.MM.YYYY');
