   public static String getCustomerInteractionType(String channel) {
        
        if ("303".equals(channel)) {
            String subSessionChannel = getCustomerInteractionTypeWithSession();
            
            if (subSessionChannel != null && isSpecialCustomerType(subSessionChannel)) {
                return subSessionChannel; // subSessionChannel BOB, KOB, VID veya CDB ise bunu return et
            } else {
                return channel; // Eğer subSessionChannel BOB, KOB, VID veya CDB değilse channel'i return et
            }
        }
        
        return null;  // Eğer channel 303 değilse subChannel'e yani customer_interaction_type hiç bakma
    }

    private static boolean isSpecialCustomerType(String subSessionChannel) {
        return "BOB".equals(subSessionChannel) ||
               "KOB".equals(subSessionChannel) ||
               "VID".equals(subSessionChannel) ||
               "CDB".equals(subSessionChannel);
    }
