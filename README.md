    paymentDTO = new PaymentDTO();
    paymentDTO.setId(123L);
    paymentDTO.setChannelCode("someChannelCode");
    paymentDTO.setContractNo(456L);
    paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);

    // Diğer mock ayarları
    when(paymentService.getPayment(anyLong(), anyLong())).thenReturn(paymentDTO);
