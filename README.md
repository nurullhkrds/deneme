DECLARE
  v_text VARCHAR2(32767); -- Maximum VARCHAR2 size for local variable
BEGIN
  FOR i IN 1..20000 LOOP
    v_text := 'Example large text for record ' || TO_CHAR(i) || ' ' || RPAD('*', 3000, '*'); -- Creating long text

    INSERT INTO BILL."tmp_REMOTE_SERVICE_LOG" (
      ID, VERSION, INSTITUTION_ID, SERVICE_TYPE, SUBSCRIBER_NO, LOG_DATE, DURATION,
      SEND_DATA, RECEIVED_DATA, INSTITUTION_RETURN_CODE, BANK_RETURN_CODE, ADDITIONAL_INFO,
      DATA_POWER_TRANSACTION_ID, CHANNEL_CODE, BRANCH_CODE, CHANNEL_TRANSACTION_ID,
      CHANNEL_SESSION_ID, CREATE_DATE, CREATED_BY, UPDATE_DATE, UPDATED_BY
    ) VALUES (
      i, -- ID
      5, -- VERSION (sabit değer)
      500, -- INSTITUTION_ID (sabit değer)
      'SERVICE', -- SERVICE_TYPE (sabit değer)
      'SUBSCRIBER', -- SUBSCRIBER_NO (sabit değer)
      SYSDATE - 100, -- LOG_DATE (sabit değer)
      60, -- DURATION (sabit değer)
      'SEND DATA', -- SEND_DATA (sabit değer)
      TO_CLOB(v_text), -- RECEIVED_DATA CLOB
      'RET_CODE', -- INSTITUTION_RETURN_CODE (sabit değer)
      'BANK_CODE', -- BANK_RETURN_CODE (sabit değer)
      'Additional Info', -- ADDITIONAL_INFO (sabit değer)
      5000, -- DATA_POWER_TRANSACTION_ID (sabit değer)
      'CH01', -- CHANNEL_CODE (sabit değer)
      'BR100', -- BRANCH_CODE (sabit değer)
      'TXN00001', -- CHANNEL_TRANSACTION_ID (sabit değer)
      'SESSION0000001', -- CHANNEL_SESSION_ID (sabit değer)
      SYSDATE, -- CREATE_DATE (sabit değer)
      'CREATOR', -- CREATED_BY (sabit değer)
      SYSDATE, -- UPDATE_DATE (sabit değer)
      'UPDATER' -- UPDATED_BY (sabit değer)
    );
  END LOOP;
  COMMIT;
END;
