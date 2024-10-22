public class StringUtils {

    public static String lpad(String input, int length, char padChar) {
        // Giriş null veya boşsa aynı şekilde döndür
        if (input == null || input.isEmpty()) {
            return input;
        }

        // 8 karakter için doldurma yap
        if (length == 8 && input.length() <= length) {
            StringBuilder sb = new StringBuilder();
            // Eksik karakter sayısı kadar padChar ekle
            for (int i = input.length(); i < length; i++) {
                sb.append(padChar);
            }
            // Orijinal input'u sona ekle
            sb.append(input);
            return sb.toString();
        }

        // 9 karakter için, sadece uzunluğu eksikse geri döndür (0 ile başlatma)
        if (length == 9 && input.length() <= length) {
            return input;
        }

        // Diğer durumlarda input olduğu gibi döndür
        return input;
    }

    public static void main(String[] args) {
        // Testler
        System.out.println(lpad("1234567", 8, '0'));  // 01234567
        System.out.println(lpad("12345678", 9, '0')); // 12345678
        System.out.println(lpad("123456", 8, '0'));   // 00123456
        System.out.println(lpad("123456789", 9, '0'));// 123456789 (başına 0 eklenmez)
        System.out.println(lpad("1234", 9, '0'));     // 1234
    }
}
