    private String createBillNo(String billDueDate,
                                String subscriberNo,
                                String debtType,
                                OdemePlaniDTO odemePlan) {

        String today = LocalDate.now().format(OUTPUT);

        if ("041".equals(debtType)) {
            String formattedDue = formatDueDate(billDueDate);
            String aboneno      = requireValue(subscriberNo, "Abone no");
            return formattedDue + aboneno + today;

        } else if ("042".equals(debtType)) {
            String aboneno = requireValue(subscriberNo, "Abone no");
            return today + aboneno + "042";

        } else if ("13".equals(debtType) || "14".equals(debtType) || "22".equals(debtType)) {
            requireNotNull(odemePlan, "Ödeme planı");
            return odemePlan.getKrediId() + odemePlan.getYil() + odemePlan.getTahsilatTuru();

        } else {
            throw new IllegalArgumentException("Geçersiz debtType: " + debtType);
        }
    }

    // ---- yardımcı küçük metotlar ----

    private String formatDueDate(String date) {
        String value = requireValue(date, "Son ödeme tarihi");
        LocalDate parsed = LocalDate.parse(value, INPUT);   // "14/08/2025"
        return parsed.format(OUTPUT);                       // "20250814"
    }

    private String requireValue(String val, String fieldName) {
        if (val == null || val.trim().isEmpty()) {
            throw new IllegalArgumentException(fieldName + " boş olamaz");
        }
        return val.trim();
    }

    private void requireNotNull(Object obj, String fieldName) {
        if (obj == null) {
            throw new IllegalArgumentException(fieldName + " boş olamaz");
        }
    }
