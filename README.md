

    private static final DateTimeFormatter INPUT =
            DateTimeFormatter.ofPattern("dd/MM/uuuu").withResolverStyle(ResolverStyle.STRICT);
    private static final DateTimeFormatter OUTPUT =
            DateTimeFormatter.ofPattern("yyyyMMdd");

    private String createBillNo(String billDueDate,
                                String subscriberNo,
                                String debtType,
                                OdemePlaniDTO odemePlan) {

        if (debtType == null || debtType.trim().isEmpty()) {
            throw new IllegalArgumentException("DebtType boş olamaz");
        }

        String today = LocalDate.now().format(OUTPUT);

        if ("041".equals(debtType)) {
            // Geçerlilik tarihi parse
            String dueFormatted = formatDueDate(billDueDate);
            String aboneno = requireDigits(subscriberNo, "Abone no boş olamaz");
            return dueFormatted + aboneno + today;

        } else if ("042".equals(debtType)) {
            String aboneno = requireDigits(subscriberNo, "Abone no boş olamaz");
            return today + aboneno + "042";

        } else if ("13".equals(debtType) || "14".equals(debtType) || "22".equals(debtType)) {
            return safe(odemePlan, "Ödeme planı boş olamaz").getKrediId()
                    + safe(odemePlan.getYil(), "Ödeme planı yıl boş olamaz")
                    + safe(odemePlan.getTahsilatTuru(), "Ödeme planı tahsilat türü boş olamaz");

        } else {
            throw new IllegalArgumentException("Bilinmeyen debtType: " + debtType);
        }
    }

    // ---- yardımcılar ----

    private static String formatDueDate(String billDueDate) {
        if (billDueDate == null || billDueDate.trim().isEmpty()) {
            throw new IllegalArgumentException("Son ödeme tarihi boş olamaz");
        }
        LocalDate d = LocalDate.parse(billDueDate.trim(), INPUT);   // "14/08/2025"
        return d.format(OUTPUT);                                   // "20250814"
    }

    private static String requireDigits(String s, String msg) {
        if (s == null || s.trim().isEmpty()) throw new IllegalArgumentException(msg);
        String t = s.trim();
        if (!t.matches("\\d+")) {
            throw new IllegalArgumentException("Abone numarası sadece rakamlardan oluşmalıdır");
        }
        return t;
    }

    private static <T> T safe(T val, String msg) {
        return Objects.requireNonNull(val, msg);
    }
}
