if (PYMStringUtils.isNotEmpty(request.getChannelCode())) {
			mappedRequest.setChannelCode(request.getChannelCode());
		} else {
			mappedRequest.setChannelCode(BillPaymentUtil.getChannel(PYMSessionUtils.getChannel()));
		}


PYMSessionUtils.getChannel() kodu
	public static String getChannel() {
		IChannelInfo channelInfo = (IChannelInfo) Session.getInstance().get(SessionKey.CHANNELINFO);
		if (channelInfo != null && channelInfo.get(ChannelInfoKey.CHANNEL) != null) {
			return channelInfo.get(ChannelInfoKey.CHANNEL);
		}
		return null;
	}




public class BillPaymentUtil {
	
	private static Map<String, String> channelMap = new HashMap<String, String>();
	
	static {
		channelMap.put("2012", "ANGI");
		channelMap.put("2032", "BIFC");
		channelMap.put("2303", "CDBI");
		channelMap.put("3401", "ADBC");
		channelMap.put("3012", "XIFCFNO");
		channelMap.put("3013", "CXNGIFNO");
		channelMap.put("4031", "FMBL");
		channelMap.put("4032", "VMBC");
		channelMap.put("4403", "BDBM");
		channelMap.put("5081", "SDATM");
		channelMap.put("6031", "DSUBE");
		channelMap.put("6705", "BTABLET");
		channelMap.put("8602", "CSMS");
		channelMap.put("15780", "BCBOT");
		channelMap.put("17480", "BCBOT");
		channelMap.put("17380", "BCBOT");
		channelMap.put("17820", "BCBOT");

	}
	
	public static String getChannel() {
		String sessionChannel = PYMSessionUtils.getChannel();
		return convertChannel(sessionChannel);
	}
	
	public static String getChannel(String channel) {
		return convertChannel(channel);
	}
	
	private static String convertChannel(String channel) {
		if (channel == null || channel.equals("")) {
			return "";
		}
		return channelMap.get(channel);
	}
}
