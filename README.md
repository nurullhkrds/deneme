public class AccountingConstant {

    public static final String C_TYPE ="BILL";
    public static final String D_TYPE ="PAY";
    public static final String DKNTSABIT = "DKNTSABIT";


    public static class CreditCardProvision {
        /**TODO:Revize edilecek*/
        private CreditCardProvision() { throw new IllegalStateException("CreditCardProvision: Constant class"); }

        public static final String CARD_PROVISION_TERM_ID = "OD19";
        public static final String CREDIT_CARD_TRANSACTION_TYPE = "PYMF";

        public static final String DO_PROVISION_SUCCESS_RESULT_CODE = "000000";

        public static final String ACKNOWLEDGE_SUCCESS_IRC = "0000000";
        public static final String ACKNOWLEDGE_TRXN_HAS_ALREADY_BEEN_SETTLED_IRC = "B968705";

    }

}
