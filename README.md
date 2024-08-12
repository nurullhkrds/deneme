public static String getSubChannel() {
    String sessionChannel = PYMSessionUtils.getChannel();
    
    if ("303" == sessionChannel) {
        String subSessionChannel = PYMSessionUtils.getSubChannel();
        
        if (subSessionChannel != null && (subSessionChannel.equals("BOB") || 
                                          subSessionChannel.equals("KOB") || 
                                          subSessionChannel.equals("VID") || 
                                          subSessionChannel.equals("CDB"))) {
            return subSessionChannel; // subSessionChannel BOB, KOB, VID veya CDB ise bunu return et
        }
    }
    
    return sessionChannel; // Eğer subSessionChannel BOB, KOB, VID veya CDB değilse ya da sessionChannel "303" değilse sessionChannel'i return et
}
