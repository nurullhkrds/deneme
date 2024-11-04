  String vadeTarihi = "2024-11-30";
        LocalDateTime vadeTarihiDate = LocalDate.parse(vadeTarihi).atStartOfDay();
        PaidBillDTO billDTO = new PaidBillDTO();
        billDTO.setInfo1("16");
        billDTO.setSubscriberName("string");
        billDTO.setInfo2("dfd");
        billDTO.setInfo3("1001#6799410");
        billDTO.setSubscriberNo("30055934852");
        billDTO.setInfo5("3");
        billDTO.setInfo6("2024#173917");
        billDTO.setInfo7("2");
        billDTO.setInfo8("31.31#0");
        billDTO.setBillDueDate(LocalDate.from(vadeTarihiDate));


        LocalDateTime tarih = LocalDateTime.of(2024, 10, 26, 0, 0, 0);


        remoteRequest.setTransactionDate(tarih);
        billDTO.setBillRecalculatedAmount(new BigDecimal(31.31).setScale(2, RoundingMode.CEILING));
        billDTO.setBankReferenceNo("dfd");
        PaymentInformationDTO paymentInformation = new PaymentInformationDTO();
        LocalDate date = LocalDate.of(2024, 10, 26);
