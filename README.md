public static String lpadWithZeros(String input, int totalLength) {
    if (input == null) {
        return null;
    }
    // Eğer gelen değer toplam uzunluktan büyükse kesme işlemi yapılabilir
    if (input.length() > totalLength) {
        return input.substring(0, totalLength);
    }
    // Başına gerekli sayıda '0' ekleme
    return String.format("%0" + totalLength + "d", Long.parseLong(input));
}
billDTO.setSubscriberNo(
    aboneBorc.getAboneNo() != null 
        ? lpadWithZeros(aboneBorc.getAboneNo().toString(), 10) 
        : null
);
