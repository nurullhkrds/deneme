
Caused by: org.hibernate.hql.internal.ast.QuerySyntaxException: REMOTE_SERVICE_LOG is not mapped [SELECT new LogRecordDTO(rsl.subscriberNo, rsl.createDate, rsl.receivedData, rsl.sendData, rsl.institutionReturnCode, rm.returnMapCode, rm.institutionReturnText, rm.bankReturnCode, inst.institutionCode, inst.productCode) FROM REMOTE_SERVICE_LOG rsl JOIN RETURN_MAP rm ON rsl.institutionReturnCode = rm.institutionReturnCode AND rsl.bankReturnCode = rm.bankReturnCode JOIN INSTITUTION inst ON rsl.institutionId = inst.id WHERE rsl.serviceType = 'NOTIFY_PAYMENT' AND inst.institutionCode = :institutionCode AND inst.productCode = :productCode AND rm.returnMapCode = :returnMapCode AND rsl.logDate BETWEEN :startDate AND :endDate]
	at org.hibernate.hql.internal.ast.QuerySyntaxException.generateQueryException(QuerySyntaxException.java:79) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.QueryException.wrapWithQueryString(QueryException.java:103) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.hql.internal.ast.QueryTranslatorImpl.doCompile(QueryTranslatorImpl.java:220) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.hql.internal.ast.QueryTranslatorImpl.compile(QueryTranslatorImpl.java:144) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.query.spi.HQLQueryPlan.<init>(HQLQueryPlan.java:112) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
	at org.hibernate.engine.query.spi.HQLQueryPlan.<init>(HQLQueryPlan.java:73) ~[hibernate-core-5.6.15.Final.jar:5.6.15.Final]
