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
		channelMap.put("201", "NGI");
		channelMap.put("202", "IFC");
		channelMap.put("203", "DBI");
		channelMap.put("301", "DBC");
		channelMap.put("302", "IFCFNO");
		channelMap.put("303", "NGIFNO");
		channelMap.put("401", "MBL");
		channelMap.put("402", "MBC");
		channelMap.put("403", "DBM");
		channelMap.put("501", "ATM");
		channelMap.put("601", "SUBE");
		channelMap.put("605", "TABLET");
		channelMap.put("802", "SMS");
		channelMap.put("1780", "CBOT");
		channelMap.put("1780", "CBOT");
		channelMap.put("1780", "CBOT");
		channelMap.put("1780", "CBOT");

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
