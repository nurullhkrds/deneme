<img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/b3423f5a-6aa9-464f-99dd-e348c2a9c7a7" />



@Named("extractOrderMethodFromPaymentOrderInfo")
default PaymentOrderInfoDTO extractOrderMethodFromPaymentOrderInfo(
        OrderInfoHmnDTO orderInfoHmnDTO) {

    if (orderInfoHmnDTO == null) {
        return null;
    }

    PaymentOrderInfoDTO paymentOrderInfoDTO = new PaymentOrderInfoDTO();

    EnumOrderMethod paymentOrderMethod =
            EnumOrderMethod.fromHmnValue(orderInfoHmnDTO.getPaymentOrderMethod());

    paymentOrderInfoDTO.setPaymentOrderMethod(paymentOrderMethod);

    if (EnumOrderMethod.ACCOUNT_LIST.equals(paymentOrderMethod)) {

        List<PaymentOrderAccountDTO> accountList = new ArrayList<>();

        if (orderInfoHmnDTO.getAccountList() != null) {
            int sequenceNo = 1;

            for (String accountNumber : orderInfoHmnDTO.getAccountList()) {
                PaymentOrderAccountDTO accountDTO =
                        new PaymentOrderAccountDTO();

                accountDTO.setAccountNo(accountNumber);
                accountDTO.setSequenceNo(sequenceNo++);

                accountList.add(accountDTO);
            }
        }

        paymentOrderInfoDTO.setAccountList(accountList);

    } else if (EnumOrderMethod.CREDIT_CARD.equals(paymentOrderMethod)) {

        paymentOrderInfoDTO.setCardNo(orderInfoHmnDTO.getCardNo());
    }

    return paymentOrderInfoDTO;
}
