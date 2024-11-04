    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
        String formattedDate = vadeTarihiDate.format(formatter);// "2024-11-30" formatında
        billDTO.setBillDueDate(LocalDate.parse(formattedDate)); // DTO'nun bu değeri alacak şekilde ayarlanmış olması gerekir

        LocalDate tarih = LocalDate.of(2024, 10, 26);
        String formattedTransactionDate = tarih.format(formatter); // "2024-10-26" formatında
        remoteRequest.setTransactionDate(LocalDateTime.parse(formattedTransactionDate)); // Eğer setTransactionDate String alıyorsa direkt atayabilirsiniz

        billDTO.setBillRecalculatedAmount(new BigDecimal(31.31).setScale(2, RoundingMode.CEILING));
        billDTO.setBankReferenceNo("dfd");
        PaymentInformationDTO paymentInformation = new PaymentInformationDTO();
        LocalDate date = LocalDate.of(2024, 10, 26);
        paymentInformation.setPaymentDate(date);
        billDTO.setPaymentInformation(paymentInformation);
