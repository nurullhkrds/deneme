@RequiredArgsConstructor
@Getter
public enum EnumOldChannels {

	TELLER("SUBE", "Vezne","V"),

	ATM("ATM", "ATM","A"),

	POS("POS", "POS",null),

	/**
	 * Bireysel İnternet Bankacılığı
	 */
	INTERNET_BANKING_RETAIL("NGI", "Bireysel \u0130nternet Bankac\u0131l\u0131\u011F\u0131","I"),

	/**
	 * Kurumsal İnternet Bankacılığı
	 */
	INTERNET_BANKING_CORPORATE("IFC", "Kurumsal \u0130nternet Bankac\u0131l\u0131\u011F\u0131","I"),

	/**
	 * NUVO İnternet Bankacılığı
	 */
	INTERNET_BANKING_NUVO("DBI", "NUVO \u0130nternet Bankac\u0131l\u0131\u011F\u0131","I"),

	/**
	 * Bireysel Mobil Bankacılık
	 */
	MOBILE_BANKING_RETAIL("MBL", "Bireysel Mobil Bankac\u0131l\u0131k","I"),

	/**
	 * Kurumsal Mobil Bankacılık
	 */
	MOBILE_BANKING_CORPORATE("MBC", "Kurumsal Mobil Bankac\u0131l\u0131k","I"),

	/**
	 * NUVO Mobil Bankacılık
	 */
	MOBILE_BANKING_NUVO("DBM", "NUVO Mobil Bankac\u0131l\u0131k","I"),

	/**
	 * Bireysel Çağrı Merkezi
	 */
	CALL_CENTER_RETAIL("NGIFNO", "Bireysel \u00C7a\u011Fr\u0131 Merkezi",null),

	/**
	 * Kurumsal Çağrı Merkezi
	 */
	CALL_CENTER_CORPORATE("IFCFNO", "Kurumsal \u00C7a\u011Fr\u0131 Merkezi",null),

	/**
	 * NUVO Çağrı Merkezi
	 */
	CALL_CENTER_NUVO("DBC", "NUVO \u00C7a\u011Fr\u0131 Merkezi",null),

	SMS("SMS", "SMS",null),

	/**
	 * Hesaptan Otomatik Ödeme İş 00
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_00("OTOH00", "Hesaptan Otomatik \u00D6deme \u0130\u015F 00","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 01
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_01("OTOH01", "Hesaptan Otomatik \u00D6deme \u0130\u015F 01","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 02
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_02("OTOH02", "Hesaptan Otomatik \u00D6deme \u0130\u015F 02","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 03
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_03("OTOH03", "Hesaptan Otomatik \u00D6deme \u0130\u015F 03","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 04
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_04("OTOH04", "Hesaptan Otomatik \u00D6deme \u0130\u015F 04","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 05
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_05("OTOH05", "Hesaptan Otomatik \u00D6deme \u0130\u015F 05","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 06
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_06("OTOH06", "Hesaptan Otomatik \u00D6deme \u0130\u015F 06","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 07
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_07("OTOH07", "Hesaptan Otomatik \u00D6deme \u0130\u015F 07","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 08
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_08("OTOH08", "Hesaptan Otomatik \u00D6deme \u0130\u015F 08","O"),

	/**
	 * Hesaptan Otomatik Ödeme İş 09
	 */
	AUTO_PAYMENT_BALANCEACCOUNT_JOB_09("OTOH09", "Hesaptan Otomatik \u00D6deme \u0130\u015F 09","O"),

	/**
	 * Kredi Kartından Otomatik Ödeme İş 01 (X)
	 */
	AUTO_PAYMENT_CREDITCARD_JOB_01("OTOKX", "Kredi Kart\u0131ndan Otomatik \u00D6deme \u0130\u015F 01 (X)","K"),

	/**
	 * Kredi Kartından Otomatik Ödeme İş 02 (Y)
	 */
	AUTO_PAYMENT_CREDITCARD_JOB_02("OTOKY", "Kredi Kart\u0131ndan Otomatik \u00D6deme \u0130\u015F 02 (Y)","K"),

	/**
	 * Kredi Kartından Otomatik Ödeme İş 03 (Z)
	 */
	AUTO_PAYMENT_CREDITCARD_JOB_03("OTOKZ", "Kredi Kart\u0131ndan Otomatik \u00D6deme \u0130\u015F 03 (Z)","K"),

	WEBSERVICE("WEB", "WebServis",null),

	/**
	 * Yönetim Uygulaması
	 */
	MANAGEMENT("SYSTEM", "Y\u00F6netim Uygulamas\u0131",null),

	/**
	 * Toplu İşlem Kanalı
	 */
	BATCH("BATCH", "Toplu \u0130\u015Flem Kanal\u0131 (Batch)",null),

	/**
	 * Monitoring Servislerinin Çalıştırılacağı Kanal
	 */
	MONITORING("MONITORING", "Monitoring Servislerinin \u00C7al\u0131\u015Ft\u0131r\u0131laca\u011F\u0131 Kanal",null),

	/**
	 * Kobi Çapraz Satış
	 */
	SME_CROSS_SELLING("KCS", "Kobi \u00E7apraz Sat\u0131\u015F",null),

	/**
	 * Web Ödeme Merkezi
	 */
	WEB_PAYMENT_CENTER("WEBODM", "Web \u00D6deme Merkezi",null),

	/**
	 * Customer On Boarding
	 */
	CUSTOMER_ON_BOARDING("COB", "Customer On Boarding",null),

	/**
	 * Tablet On Boarding
	 */
	TABLET_ON_BOARDING("TOB", "Tablet On Boarding",null),

	TABLET("TABLET", "Tablet",null),

	TABLET_MIM("TABLETMIM", "Tablet Mim",null),

	/**
	 * DOB Çapraz Satış
	 */
	DOB_CROSS_SELLING("DOB", "DOB \u00E7apraz Sat\u0131\u015F",null),

	/**
	 * Secure Chat Bot
	 */
	SECURE_CHAT_BOT("CBOT", "Secure Chat Bot",null);

	private final String value;

	private final String description;

	private final String code;


	private static Map<String, EnumOldChannels> eMap;

	/**
	 * Static initializer
	 */
	static {
		eMap = new TreeMap<>();
		for (EnumOldChannels num : EnumOldChannels.values()) {
			eMap.put(num.getValue(), num);
		}
	}

	public static EnumOldChannels parseValue(String value) {
		return (value != null && eMap.containsKey(value)) ? eMap.get(value) : null;
	}

}
