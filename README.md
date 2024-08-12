	public static String getCustomerInteractionType(String channel) {
	    
	    if (channel.equals("303")) {
	        String subSessionChannel = getCustomerInteractionTypeWithSession();
	        
	        if (subSessionChannel != null
	        		&& (subSessionChannel.equals("BOB") ||
	        				subSessionChannel.equals("KOB") ||
	        				subSessionChannel.equals("VID") ||
	        				subSessionChannel.equals("CDB"))) {
	            return subSessionChannel; // subSessionChannel BOB, KOB, VID veya CDB ise bunu return et
	        }
	        else{
	        	return channel;// Eğer subSessionChannel BOB, KOB, VID veya CDB değilse channelli return et
	        }
	    }
	    
	    return null;  //eğer channel 303 değilse subChannel'e yani customer_interaction_type hiç bakma
	}

Reduce the number of conditional operators (4) used in the expression (maximum allowed 3).
