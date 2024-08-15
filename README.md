	public static String getCustomerInteractionTypeWithSession() {
		IChannelInfo channelInfo = (IChannelInfo) Session.getInstance().get(SessionKey.CHANNELINFO);
		String customerInteractionType = null;
		if (channelInfo != null) {
			customerInteractionType = channelInfo.get(ChannelInfoKey.CUSTOMER_INTERACTION_TYPE);
			return customerInteractionType;
		}
		return null;
	}
