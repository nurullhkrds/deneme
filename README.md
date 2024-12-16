AND pslog.LOG_DATE BETWEEN TO_DATE(:P_START_DATE, 'yyyy-MM-dd') 
                       AND TO_DATE(:P_END_DATE, 'yyyy-MM-dd')
