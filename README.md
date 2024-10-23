BigDecimal toplamOdeme = 
    (remoteRequest.getTotalPaymentAmount() != null ? remoteRequest.getTotalPaymentAmount() : BigDecimal.ZERO)
    .add(remoteRequest.getTotalPaymentCancelAmount() != null ? remoteRequest.getTotalPaymentCancelAmount() : BigDecimal.ZERO);
