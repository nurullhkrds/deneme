public final class CurrencyUtil {

    public static String currencyConverter(String currency) {
        if (currency == null || currency.equals(EnumCurrencyCode.TURKISH_LIRA.getValue()) || currency.equals(EnumCurrencyCode.TURKISH_LIRA_TRY.getValue())) {
            return EnumCurrencyCode.TURKISH_LIRA_YTL.getValue();
        }
        return currency;
    }
}
