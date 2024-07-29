package com.ykb.payments.bill.transaction.adapter.core.utilities;



public class Result {
    private boolean success;
    private String message;
    private int statusCode;

    public Result(boolean success, String message, int statusCode) {
        this.success = success;
        this.message = message;
        this.statusCode = statusCode;
    }

    public boolean isSuccess() {
        return this.success;
    }

    public String getMessage() {
        return this.message;
    }

    public int getStatusCode() {
        return this.statusCode;
    }
}

package com.ykb.payments.bill.transaction.adapter.core.utilities;



public class DataResult<T> extends Result {
    private T data;

    public DataResult(boolean success, String message, T data, int statusCode) {
        super(success, message, statusCode);
        this.data = data;
    }



    public T getData() {
        return this.data;
    }


}
