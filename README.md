DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");

String vadeTarihi = "2024-11-30";
LocalDate vadeTarihiDate = LocalDate.parse(vadeTarihi, formatter); // Sadece tarih kısmı

illDTO.setBillDueDate(vadeTarihiDate);

LocalDate tarih = LocalDate.of(2024, 10, 26);
remoteRequest.setTransactionDate(tarih); // LocalDate kullanarak yalnızca yıl-ay-gün gönderiliyor
LocalDate date = LocalDate.of(2024, 10, 26);
paymentInformation.setPaymentDate(date); 
