String tahsilatTarihi = "2024-10-26T00:00:00"; // Saat bilgisini ekleyin
LocalDateTime transactionDate = LocalDateTime.parse(tahsilatTarihi);
remoteRequest.setTransactionDate(transactionDate);
