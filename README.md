    private static final Set<String> SPECIAL_CUSTOMER_TYPES = new HashSet<>(Arrays.asList("BOB", "KOB", "VID", "CDB"));

    public static String getCustomerInteractionType(String channel) {
        
        if ("303".equals(channel)) {
            String subSessionChannel = getCustomerInteractionTypeWithSession();
            
            if (subSessionChannel != null && SPECIAL_CUSTOMER_TYPES.contains(subSessionChannel)) {
                return subSessionChannel; // subSessionChannel BOB, KOB, VID veya CDB ise bunu return et
            } else {
                return channel; // Eğer subSessionChannel BOB, KOB, VID veya CDB değilse channel'i return et
            }
        }
        
        return null;  // Eğer channel 303 değilse subChannel'e yani customer_interaction_type hiç bakma
    }
