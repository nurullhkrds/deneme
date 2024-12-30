DECLARE
  -- Cursor tanımı
  CURSOR c_remote_service_log IS
    SELECT 
      ID, VERSION, INSTITUTION_ID, SERVICE_TYPE, SUBSCRIBER_NO, LOG_DATE, DURATION,
      SEND_DATA, RECEIVED_DATA, INSTITUTION_RETURN_CODE, BANK_RETURN_CODE, ADDITIONAL_INFO,
      DATA_POWER_TRANSACTION_ID, CHANNEL_CODE, BRANCH_CODE, CHANNEL_TRANSACTION_ID,
      CHANNEL_SESSION_ID, CREATE_DATE, CREATED_BY, UPDATE_DATE, UPDATED_BY
    FROM 
      BILL."tmp_REMOTE_SERVICE_LOG";
  
  -- Record türü tanımı, sütunların belirlenmesi
  TYPE t_remote_service_log_rec IS TABLE OF c_remote_service_log%ROWTYPE INDEX BY PLS_INTEGER;
  v_remote_service_log_tab t_remote_service_log_rec;

BEGIN
  OPEN c_remote_service_log;
  LOOP
    FETCH c_remote_service_log BULK COLLECT INTO v_remote_service_log_tab LIMIT 10000; -- Her döngüde 10,000 kayıt toplanır
    EXIT WHEN v_remote_service_log_tab.COUNT = 0; -- Veri kalmadığında döngüden çıkılır

    -- FORALL ile hızlı veri ekleme
    FORALL i IN 1 .. v_remote_service_log_tab.COUNT
      INSERT INTO BILL.REMOTE_SERVICE_LOG (
        ID, VERSION, INSTITUTION_ID, SERVICE_TYPE, SUBSCRIBER_NO, LOG_DATE, DURATION,
        SEND_DATA, RECEIVED_DATA, INSTITUTION_RETURN_CODE, BANK_RETURN_CODE, DATAPOWER_GLOBAL_ID,
        DATA_POWER_TRANSACTION_ID, CHANNEL_CODE, BRANCH_CODE, CHANNEL_TRANSACTION_ID,
        CHANNEL_SESSION_ID, CREATE_DATE, CREATED_BY, UPDATE_DATE, UPDATED_BY
      ) VALUES (
        v_remote_service_log_tab(i).ID, v_remote_service_log_tab(i).VERSION, v_remote_service_log_tab(i).INSTITUTION_ID, v_remote_service_log_tab(i).SERVICE_TYPE,
        v_remote_service_log_tab(i).SUBSCRIBER_NO, v_remote_service_log_tab(i).LOG_DATE, v_remote_service_log_tab(i).DURATION,
        v_remote_service_log_tab(i).SEND_DATA, v_remote_service_log_tab(i).RECEIVED_DATA, v_remote_service_log_tab(i).INSTITUTION_RETURN_CODE, v_remote_service_log_tab(i).BANK_RETURN_CODE,
        v_remote_service_log_tab(i).ADDITIONAL_INFO, -- ADDITIONAL_INFO'dan DATAPOWER_GLOBAL_ID'ye aktarım
        v_remote_service_log_tab(i).DATA_POWER_TRANSACTION_ID, v_remote_service_log_tab(i).CHANNEL_CODE, v_remote_service_log_tab(i).BRANCH_CODE, v_remote_service_log_tab(i).CHANNEL_TRANSACTION_ID,
        v_remote_service_log_tab(i).CHANNEL_SESSION_ID, v_remote_service_log_tab(i).CREATE_DATE, v_remote_service_log_tab(i).CREATED_BY, v_remote_service_log_tab(i).UPDATE_DATE, v_remote_service_log_tab(i).UPDATED_BY
      );
    
    COMMIT; -- Her parti işlemden sonra veritabanına commit yapılır
  END LOOP;
  CLOSE c_remote_service_log;
END;
