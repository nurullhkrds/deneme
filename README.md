[EXCEPTION SOURCE INFORMATION]
	File: SQLExceptionSubclassTranslator.java
	Class: org.springframework.jdbc.support.SQLExceptionSubclassTranslator
	Method: doTranslate
	Line Number: 74
 
[EXCEPTION STACK TRACE]
com.ykb.hmn.inf.core.exception.internal.HmnException:  ( 10014 ) Unknown Exception at [bse.v2.TransactionInternalService]-[performTransactionList], jvmId: [340], transactionId:[3403477396346396] .Root Cause: [org.springframework.dao.QueryTimeoutException: CallableStatementCallback; SQL [{call HMN_TELLERTRANSACTION_V2.PERFORMTRANSACTION(?, ?, ?, ?)}]; ORA-01013: user requested cancel of current operation
; nested exception is java.sql.SQLTimeoutException: ORA-01013: user requested cancel of current operation
]
	at org.springframework.jdbc.support.SQLExceptionSubclassTranslator.doTranslate(SQLExceptionSubclassTranslator.java:74)
	at org.springframework.jdbc.support.AbstractFallbackSQLExceptionTranslator.translate(AbstractFallbackSQLExceptionTranslator.java:73)
	at org.springframework.jdbc.support.AbstractFallbackSQLExceptionTranslator.translate(AbstractFallbackSQLExceptionTranslator.java:81)
	at org.springframework.jdbc.core.JdbcTemplate.execute(JdbcTemplate.java:1099)
	at org.springframework.jdbc.core.JdbcTemplate.call(JdbcTemplate.java:1135)
	at com.ykb.hmn.inf.core.dao.HarmoniStoredProcedure.execute(HarmoniStoredProcedure.java:362)
