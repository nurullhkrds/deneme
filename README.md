package com.ykb.payments.bill.transaction.common.exception;

import com.ykb.architecture.micro.error.exception.data.ExceptionData;

public class PaymentExceptions {

	private PaymentExceptions() {
		throw new IllegalStateException("PaymentExceptions: Constant class");
	}

	public static final String SHORT_APP_NAME = "aaaa-transaction";
	
    public static final ExceptionData SUCCESS = new ExceptionData(aaa, 4000, "aaa");


	public static class ValidationExceptions {

        private ValidationExceptions() {
            throw new IllegalStateException("ValidationExceptions: Constant class");
        }

        public static final ExceptionData SERVICE_DEFINITION_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData TOKEN_RECORD_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData PARAMETER_RECORD_NOT_FOUND =new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData TOKEN_REFRESH_UNKNOWN_ERROR = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData MINUS_PAYMENT_AMOUNT =new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData INVALID_PAYMENT_AMOUNT_FORMAT = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData ACCOUNT_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData CREDIT_CARD_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData NOT_NULL_ACCOUNT_FOR_CREDIT_CARD_SOURCE = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData NOT_NULL_CREDIT_CARD_FOR_ACCOUNT_SOURCE = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData SEQUENCE_NOT_FOUND =new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData PACKAGE_GROUP_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData PACKAGES_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData INQUIRY_TYPE_NOT_SUPPORTED = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData SALE_CHANNEL_IS_INVALID = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData CHANNEL_CODE_IS_EMPTY = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData INSTITUTION_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData PAYMENT_METHOD_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData INSTITUTION_CHANNEL_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData INSTITUTION_CHANNEL_PAYMENT_METHOD_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData INSTITUTION_PAYMENT_METHOD_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData INSTITUTION_ACCOUNT_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData PAYMENT_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData PAYMENT_NOTIFICATION_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData UNSUPPORTED_PAYMENT_METHOD_TYPE = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData EXPENSE_CODE_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData COMMISSION_AMOUNT_NOT_MATCHED = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData DEALER_MIN_PAYMENT_AMOUNT =new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData DEALER_MAX_PAYMENT_AMOUNT = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData INSTITUTION_PROCESS_IS_OUT_OF_WORKING_HOURS = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData INSTITUTION_PACKAGE_CODE_EXIST = new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData INSTITUTION_PACKAGE_GROUP_DEFINITION_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData INSTITUTION_PACKAGE_DEFINITION_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData ERROR_MAPPING_EXISTS = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData ERROR_MAPPING_NOT_EXISTS =new ExceptionData(aaa, 4000, "aaa");
        public static final ExceptionData USER_CODE_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData ID_NOT_FOUND = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData INSTITUTION_SERVICE_PARAMETER_EXISTS = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData INSTITUTION_SERVICE_PARAMETER_NOT_FOUND = nnew ExceptionData(aaa, 4000, "aaa");

    }
   //TODO: EnumBillResult a tasi
    public static class AccountingExceptions {

        private AccountingExceptions() {
            throw new IllegalStateException("AccountingExceptions: Constant class");
        }

        public static final ExceptionData COMMISSION_NOT_FOUND =new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData COMMISSION_GENERIC_UNKNOWN_ERROR =new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData MERCHANT_TYPE_IS_INVALID = new ExceptionData(aaa, 4000, "aaa");

        public static final ExceptionData UNKNOWN_CREDIT_CARD_PROVISION_VERSION =new ExceptionData(aaa, 4000, "aaa");
        
        public static final ExceptionData CREDIT_CARD_PROVISION_ACK_NOTIFICATION_SUCCESS =new ExceptionData(aaa, 4000, "aaa");
        
        public static final ExceptionData CREDIT_CARD_REVERSE_PROVISION_NOTIFICATION_SUCCESS = new ExceptionData(aaa, 4000, "aaa");


        
    }

}
