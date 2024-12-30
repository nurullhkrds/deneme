DECLARE
  -- Cursor tanımı
  CURSOR c_remote_service_log IS
    SELECT * FROM BILL."tmp_REMOTE_SERVICE_LOG";
  
  -- Records tipi tanımlama
  TYPE t_remote_service_log_tab IS TABLE OF BILL."tmp_REMOTE_SERVICE_LOG"%ROWTYPE;
  v_remote_service_log_tab t_remote_service_log_tab;

BEGIN
  -- BULK COLLECT ile verileri topluca al
  OPEN c_remote_service_log;
  LOOP
    FETCH c_remote_service_log BULK COLLECT INTO v_remote_service_log_tab LIMIT 10000;
    
    EXIT WHEN v_remote_service_log_tab.COUNT = 0;
    
    -- FORALL ile hızlı insert
    FORALL i IN 1..v_remote_service_log_tab.COUNT
      INSERT INTO BILL.REMOTE_SERVICE_LOG VALUES v_remote_service_log_tab(i);
    
    COMMIT;
  END LOOP;
  CLOSE c_remote_service_log;
END;
