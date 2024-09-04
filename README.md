public class ChannelCodeValidator implements ConstraintValidator<Channel, String> {

	@Override
	public boolean isValid(String channelCode, ConstraintValidatorContext context) {
		return StringUtils.isNotEmpty(channelCode) && EnumChannel.parseValue(channelCode) != null;
	}

}
