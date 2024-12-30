BEGIN
    FOR i IN 1..13000 LOOP
        INSERT INTO BILL."tmp_REMOTE_SERVICE_LOG" (
            ID, VERSION, INSTITUTION_ID, SERVICE_TYPE, SUBSCRIBER_NO, LOG_DATE, DURATION,
            SEND_DATA, RECEIVED_DATA, INSTITUTION_RETURN_CODE, BANK_RETURN_CODE, ADDITIONAL_INFO,
            DATA_POWER_TRANSACTION_ID, CHANNEL_CODE, BRANCH_CODE, CHANNEL_TRANSACTION_ID,
            CHANNEL_SESSION_ID, CREATE_DATE, CREATED_BY, UPDATE_DATE, UPDATED_BY
        ) VALUES (
            i, 1, MOD(i, 100) + 1, 'ServiceType'||TO_CHAR(MOD(i, 10)+1),
            'Subscriber'||TO_CHAR(i), SYSDATE, MOD(i, 60) + 1,
            'SendData'||TO_CHAR(i), 'ReceivedData'||TO_CHAR(i), 'ReturnCode'||TO_CHAR(MOD(i, 5)+1),
            'BankCode'||TO_CHAR(MOD(i, 5)+1), 'AdditionalInfo'||TO_CHAR(i),
            i, 'CH'||LPAD(TO_CHAR(MOD(i, 10)+1), 2, '0'), 'BR'||LPAD(TO_CHAR(MOD(i, 100)+1), 3, '0'),
            'TXN'||TO_CHAR(i), 'Session'||TO_CHAR(i), SYSDATE, 'User'||TO_CHAR(MOD(i, 10)+1),
            SYSDATE, 'User'||TO_CHAR(MOD(i, 10)+1)
        );
    END LOOP;
    COMMIT;
END;
