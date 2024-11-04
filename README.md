String vadeTarihi = "2024-11-30";
LocalDate vadeTarihiDate = LocalDate.parse(vadeTarihi); // Sadece yıl-ay-gün

PaidBillDTO billDTO = new PaidBillDTO();
billDTO.setInfo1("16");
billDTO.setSubscriberName("string");
billDTO.setInfo2("dfd");
billDTO.setInfo3("1001#6799410");
billDTO.setInfo5("3");
billDTO.setInfo7("2");
billDTO.setInfo8("31.31#0");
billDTO.setBillDueDate(vadeTarihiDate); // LocalDate kullanarak sadece yıl-ay-gün

LocalDate tarih = LocalDate.of(2024, 10, 26); // Sadece yıl-ay-gün
remoteRequest.setTransactionDate(tarih.atStartOfDay()); // Eğer saat olmadan kullanılması gerekiyorsa sadece tarih kullanılır

billDTO.setBillRecalculatedAmount(new BigDecimal("31.31").setScale(2, RoundingMode.CEILING));
billDTO.setBankReferenceNo("dfd");

PaymentInformationDTO paymentInformation = new PaymentInformationDTO();
LocalDate date = LocalDate.of(2024, 10, 26); // Sadece yıl-ay-gün
