import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

String vadeTarihi = "2024-11-30";
LocalDate vadeTarihiDate = LocalDate.parse(vadeTarihi);

PaidBillDTO billDTO = new PaidBillDTO();
billDTO.setInfo1("16");
billDTO.setSubscriberName("string");
billDTO.setInfo2("dfd");
billDTO.setInfo3("1001#6799410");
billDTO.setInfo5("3");
billDTO.setInfo7("2");
billDTO.setInfo8("31.31#0");

// Biçimlendiriciyle yalnızca yıl-ay-gün formatında tarih oluştur
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
String formattedDate = vadeTarihiDate.format(formatter); // "2024-11-30" formatında

// formattedDate'i doğrudan String olarak atayabilirsiniz veya DTO'da bu String formatını kullanabilirsiniz
billDTO.setBillDueDate(formattedDate); // DTO'nun bu değeri alacak şekilde ayarlanmış olması gerekir

LocalDate tarih = LocalDate.of(2024, 10, 26);
String formattedTransactionDate = tarih.format(formatter); // "2024-10-26" formatında
remoteRequest.setTransactionDate(formattedTransactionDate); // Eğer setTransactionDate String alıyorsa direkt atayabilirsiniz
