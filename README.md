import java.time.format.DateTimeFormatter;

// DateTimeFormatter ile tarih formatını belirleyin
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");

// Tarihi formatlayın
String formattedDate = paidBillDTO.getPaymentInformation().getPaymentDate().format(formatter);
getArmadasService().borcOde(INSTITUTION_CODE,
                paidBillDTO.getSubscriberNo(),
                keyValue,
                paidBillDTO.getBillAmount() != null ? paidBillDTO.getBillAmount().toString() : null,
                formattedDate,  // Burada formatlanmış tarihi kullanın
                EnumCurrencyCodes.TURKISH_LIRA_TL.getValue());
