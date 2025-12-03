public static String maskNameSurname(String fullName) {
    if (fullName == null) {
        return null;
    }

    String trimmed = fullName.trim();
    if (trimmed.isEmpty()) {
        return trimmed;
    }

    char[] chars = trimmed.toCharArray();
    boolean newWord = true;

    for (int i = 0; i < chars.length; i++) {
        char c = chars[i];

        if (Character.isWhitespace(c)) {
            // boşluk gördük, bir sonraki harf yeni kelimenin başı olacak
            newWord = true;
        } else {
            if (newWord) {
                // kelimenin ilk harfini olduğu gibi bırak
                newWord = false;
            } else {
                // kelimenin geri kalanını * yap
                chars[i] = '*';
            }
        }
    }

    return new String(chars);
}



public static String maskKrediNo(String krediNo, String debtTypeCode) {
    final String KONUT_KREDISI = "22";

    // Burada contains yerine equals daha mantıklı ve hızlı
    if (KONUT_KREDISI.equals(debtTypeCode)) {
        // konut kredisi için özel durumun neyse onu yap
        // örnek: hiç göstermemek istiyorsan:
        // return "************";
        return krediNo; // sadece örnek
    }

    if (krediNo == null) {
        return null;
    }

    krediNo = krediNo.trim();
    int len = krediNo.length();
    if (len == 0) {
        return krediNo;
    }

    char[] out = krediNo.toCharArray();  // doğrudan bunun üstünde oynayalım

    if (len == 11) {
        // 0..4 ve 9.. len-1 görünür, arası maskeli
        for (int i = 0; i < len; i++) {
            boolean visible = (i <= 4) || (i >= 9);
            if (!visible) {
                out[i] = '*';
            }
        }
    } else {
        // genel kural: ilk 4 ve son 2 kalsın, arası maskeli
        int leftVisible  = Math.min(4, len - 1);   // güvenlik için
        int rightVisible = Math.min(2, len - 1);

        int rightStart = len - rightVisible;

        for (int i = 0; i < len; i++) {
            boolean visible = (i <= leftVisible) || (i >= rightStart);
            if (!visible) {
                out[i] = '*';
            }
        }
    }

    return new String(out);
}
