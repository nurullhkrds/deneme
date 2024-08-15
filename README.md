public static String getCustomerInteractionTypeWithSession() {
    IChannelInfo channelInfo = (IChannelInfo) Session.getInstance().get(SessionKey.CHANNELINFO);
    String customerInteractionType = null;
    if (channelInfo != null) {
        customerInteractionType = channelInfo.get(ChannelInfoKey.CUSTOMER_INTERACTION_TYPE);
        // Numeric olmayan değerler için kontroller yapın
        if (customerInteractionType != null) {
            try {
                // Eğer değer sayısal değilse, hata verecek ve catch bloğu çalışacaktır
                Integer.parseInt(customerInteractionType);
            } catch (NumberFormatException e) {
                // Bu durumda "BOB" gibi bir değer dönebilir ve bu değeri handle etmeniz gerekir
                return customerInteractionType; // Ya da uygun bir işleme yönlendirin
            }
        }
        return customerInteractionType;
    }
    return null;
}
