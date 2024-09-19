blic class BillTransactionException extends RuntimeException{

    public BillTransactionException() {
        super();
    }

    public BillTransactionException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace) {
        super(message, cause, enableSuppression, writableStackTrace);
    }

    public BillTransactionException(String message, Throwable cause) {
        super(message, cause);
    }

    public BillTransactionException(String message) {
        super(message);
    }

    public BillTransactionException(Throwable cause) {
        super(cause);
    }
}
