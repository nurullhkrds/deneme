[EXCEPTION SOURCE INFORMATION]
	File: AbstractFallbackSQLExceptionTranslator.java
	Class: org.springframework.jdbc.support.AbstractFallbackSQLExceptionTranslator
	Method: translate
	Line Number: 84

[EXCEPTION STACK TRACE]
com.ykb.hmn.inf.core.exception.internal.HmnException:  ( 10014 ) Unknown Exception at [bse.v2.TransactionInternalService]-[performTransactionList], jvmId: [451], transactionId:[4517780741410190] .Root Cause: [org.springframework.jdbc.UncategorizedSQLException: CallableStatementCallback; uncategorized SQLException for SQL [{call HMN_TELLERTRANSACTION_V2.PERFORMTRANSACTION(?, ?, ?, ?)}]; SQL state [72000]; error code [4063]; ORA-04063: package body "CBSLIVE.CBS_ACCOUNTS" has errors
ORA-06508: PL/SQL: could not find program unit being called: "CBSLIVE.CBS_ACCOUNTS"
ORA-06512: at "CBSLIVE.L012_CHK_BAL", line 142
ORA-06512: at "CBSLIVE.HMN_TELLERTRANSACTION_V2", line 1630
ORA-06512: at line 1
; nested exception is java.sql.SQLException: ORA-04063: package body "CBSLIVE.CBS_ACCOUNTS" has errors
ORA-06508: PL/SQL: could not find program unit being called: "CBSLIVE.CBS_ACCOUNTS"
ORA-06512: at "CBSLIVE.L012_CHK_BAL", line 142
ORA-06512: at "CBSLIVE.HMN_TELLERTRANSACTION_V2", line 1630
ORA-06512: at line 1
