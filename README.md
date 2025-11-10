public final class MaskingUtil {

    private MaskingUtil() {}

    // ==== 1) KUN (toplam uzunluk 7; 1. ve 7. haneyi açık bırak) ====
    public static String maskKun(String kun) {
        if (kun == null) return null;
        kun = kun.trim();
        if (kun.length() == 0) return kun;

        // Kural: 1 ve 7 açık; diğeri '*'
        // Uzunluk 7 değilse, yine de 1. ve son haneyi açık bırakıp geri kalanını maskeleyelim.
        int len = kun.length();
        char[] out = new char[len];

        for (int i = 0; i < len; i++) {
            boolean visible = (i == 0) || (len == 7 && i == 6) || (len != 7 && i == len - 1);
            out[i] = visible ? kun.charAt(i) : '*';
        }
        return new String(out);
    }

    // ==== 2) AD SOYAD (birden fazla kelime varsa her birinin İLK harfi açık; kalanlar maskeli) ====
    public static String maskNameSurname(String fullName) {
        if (fullName == null) return null;

        // Fazla boşlukları normalize edelim (birden fazla space -> tek space)
        String trimmed = fullName.trim();
        if (trimmed.length() == 0) return trimmed;

        String[] parts = trimmed.split("\\s+");
        StringBuilder sb = new StringBuilder();

        for (int p = 0; p < parts.length; p++) {
            String word = parts[p];
            if (word.length() == 0) {
                // arada garip boşluk varsa atla
            } else {
                sb.append(word.charAt(0));
                for (int i = 1; i < word.length(); i++) {
                    // harf, rakam, noktalama gözetmeden kalan tüm karakterleri maskele
                    sb.append('*');
                }
            }
            if (p < parts.length - 1) sb.append(' ');
        }
        return sb.toString();
    }

    // ==== 3) Kredi No (toplam uzunluk 11; ilk 5 ve 10-11 açık, 6-9 maskeli) ====
    public static String maskKrediNo(String krediNo) {
        if (krediNo == null) return null;
        krediNo = krediNo.trim();
        int len = krediNo.length();
        if (len == 0) return krediNo;

        char[] out = new char[len];
        for (int i = 0; i < len; i++) {
            boolean visible;
            if (len == 11) {
                // 1-5 ve 10-11 açık (index 0-4 ve 9-10)
                visible = (i <= 4) || (i >= 9);
            } else {
                // Uzunluk 11 değilse: makul bir fallback -> ilk 5 açık, son 2 açık, arası maskeli
                visible = (i <= Math.min(4, len - 1)) || (i >= Math.max(0, len - 2));
            }
            out[i] = visible ? krediNo.charAt(i) : '*';
        }
        return new String(out);
    }

    // ---- İsteğe bağlı: genel amaçlı pozisyon bazlı maskeleme (1-based index) ----
    public static String maskByVisiblePositions(String input, int[] visiblePositions1Based) {
        if (input == null) return null;
        int len = input.length();
        char[] out = new char[len];

        for (int i = 0; i < len; i++) {
            boolean visible = false;
            for (int vp : visiblePositions1Based) {
                if (vp == (i + 1)) { visible = true; break; }
            }
            out[i] = visible ? input.charAt(i) : '*';
        }
        return new String(out);
    }
}
