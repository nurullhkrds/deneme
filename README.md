public class ChannelUtil {

	private ChannelUtil() {
		throw new IllegalAccessError("util class");
	}

	private static Map<String, String> channelMap = new HashMap<String, String>();
	private static Map<String, String> cardEventDescChannelMap = new HashMap<String, String>();

	static {
		channelMap.put("NGI", "201");
		channelMap.put("IFC", "202");
		channelMap.put("DBI", "203");
		channelMap.put("DBC", "301");
		channelMap.put("IFCFNO", "302");
		channelMap.put("NGIFNO", "303");
		channelMap.put("WAP", "401"); // Because two different old channel code mapped to same new one we can not make
										// reverse mapping for both.
		channelMap.put("MBL", "401");
		channelMap.put("MBC", "402");
		channelMap.put("DBM", "403");
		channelMap.put("ATM", "501");
		channelMap.put("SUBE", "601");
		channelMap.put("TABLET", "605");
		channelMap.put("BATCH", "602");
		channelMap.put("WEBODM", "702");
		channelMap.put("SMS", "801");
		channelMap.put("POS", "901");
		channelMap.put("DOB", "959");
		channelMap.put("CBOT", "1780");
		channelMap.put("TABLETMIM", "606");
		channelMap.put("BOB", "304");
		channelMap.put("KOB", "305");
		channelMap.put("VID", "306");

		channelMap.put("KCS", "KCS");
		channelMap.put("TOB", "TOB");
		channelMap.put("COB", "COB");
		
		cardEventDescChannelMap.put("501", "ATM");
		cardEventDescChannelMap.put("201", "INTERNET");
		cardEventDescChannelMap.put("202", "INTERNET");
		cardEventDescChannelMap.put("203", "INTERNET");
		cardEventDescChannelMap.put("401", "INTERNET");
		cardEventDescChannelMap.put("402", "INTERNET");
		cardEventDescChannelMap.put("403", "INTERNET");
		cardEventDescChannelMap.put("303", "INTERNET");
		cardEventDescChannelMap.put("302", "INTERNET");
		cardEventDescChannelMap.put("301", "INTERNET");
		cardEventDescChannelMap.put("702", "INTERNET");
	}

	public static String convertChannel(String channelCode) {
		if (StringUtils.isEmpty(channelCode)) {
			return "";
		}

		if (!isHarmoniChannel(channelCode)) {
			return channelCode.length() > 4 ? channelCode.substring(0, 4) : channelCode;
		}

		return channelMap.get(channelCode);
	}
	
	public static String convertToHarmoniChannel(String channelCode) {
		if (StringUtils.isEmpty(channelCode)) {
			return "";
		}

		if (isHarmoniChannel(channelCode)) {
			return channelCode;
		}

		for (Entry<String, String> entry : channelMap.entrySet()) {
			if (entry.getValue().equals(channelCode)) {
				return entry.getKey();
			}
		}

		return "";
	}

	private static boolean isHarmoniChannel(String channelCode) {
		return channelMap.keySet().contains(channelCode);
	}
	
	public static String convertCardEventDescChannel(String channelCode) {
		if (StringUtils.isEmpty(channelCode)) {
			return "";
		}		

		return cardEventDescChannelMap.get(channelCode);
	}
}
