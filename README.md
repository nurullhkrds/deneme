	public static String getCustomerInteractionType(String channel) {
		String channelCode = convertChannel(channel);
		if (channelCode != null
				&& (channelCode.equals("303") || channelCode.equals("301") || channelCode.equals("302"))) {
			return  convertChannel(getCustomerInteractionTypeWithSession());

		}

		return null;
	}
