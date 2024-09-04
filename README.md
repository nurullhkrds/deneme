public class JsonUtil {
	private static final Logger LOGGER = LoggerFactory.getLogger(JsonUtil.class);
	
	public static String convertObjectToJsonString(Object object) throws JsonProcessingException {
		ObjectMapper mapper = new ObjectMapper();
		mapper.registerModule(new JavaTimeModule());
		//XMLGregorianCalendar type converts to millisecond such as 2011-05-31T00:00:00+03:00 to => 1338411600000.
		//That's unreadable at the online_service_logs table.
		mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));


		return mapper.writeValueAsString(object);
	}
	
    public static <T> T convertStringToObject(String rawJson, Class<T> clazz) throws JsonProcessingException {
        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.registerModule(new JavaTimeModule());
        return objectMapper.readValue(rawJson, clazz);
    }
    
    
	public static String convertObjectToJsonStringWithoutException(Object object){
		ObjectMapper mapper = new ObjectMapper();
		mapper.registerModule(new JavaTimeModule());
		mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
		mapper.setSerializationInclusion(Include.NON_NULL);


		try {
			return mapper.writeValueAsString(object);
		} catch (JsonProcessingException e) {
			return "";
		}
	}
}
