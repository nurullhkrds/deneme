 public static BigDecimal toBigDecimal(String value) {
        if (value == null || value.trim().isEmpty()) {
            return null;
        }

        // Tüm virgülleri temizle
        String normalized = value.trim().replace(",", "");

        try {
            return new BigDecimal(normalized);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Geçersiz sayı formatı: " + value, e);
        }
    }
