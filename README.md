public String olusturFaturaNo(int sistemId, int hesapId, int beyanAnaId, int beyanSiraNo, int yil, int taksit, LocalDate vadeTarihi) {
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd");
    String vadeTarihiStr = vadeTarihi.format(formatter);
    return sistemId + "-" + hesapId + "-" + beyanAnaId + "-" + beyanSiraNo + "-" + yil + "-" + taksit + "-" + vadeTarihiStr;
}
