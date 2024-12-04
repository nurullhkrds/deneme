getArmadasService().borcOde(INSTITUTION_CODE,
                paidBillDTO.getSubscriberNo(),
                keyValue,
                paidBillDTO.getBillAmount() != null ? paidBillDTO.getBillAmount().toString() : null,
                paidBillDTO.getPaymentInformation().getPaymentDate().format(),
                EnumCurrencyCodes.TURKISH_LIRA_TL.getValue() );


String tarih,
