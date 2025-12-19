private String normalizeAccountNo(String accountNo) {
    if (accountNo == null) {
        return null;
    }

    String trimmed = accountNo.trim();

    // 8 haneden kısa ise sola sıfır ekle
    if (trimmed.length() < 8) {
        String padded = String.format("%08d", Long.parseLong(trimmed));
        return padded.replaceFirst("^0+", "");
    }

    return trimmed;
}


String normalizedAccNo = normalizeAccountNo(gPostAccNum);

checkHesapKurum(
        trxCode,
        branchCode,
        reverse,
        normalizedAccNo,
        errCode,
        errText
);
