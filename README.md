Integer toplamOdemeSayisi = 
    (remoteRequest.getTotalOrderCancelCount() != null ? remoteRequest.getTotalOrderCancelCount() : 0) +
    (remoteRequest.getTotalPaymentCount() != null ? remoteRequest.getTotalPaymentCount() : 0);
