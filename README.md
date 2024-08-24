@Mapper(componentModel = "spring")
public interface PaymentNotificationMapper {

	PaymentNotificationMapper INSTANCE = Mappers.getMapper(PaymentNotificationMapper.class);
	
	PaymentNotification toEntity(PaymentNotificationDTO dto);

	PaymentNotificationDTO toDTO(PaymentNotification entity);
}

@Mapper(componentModel = "spring")
public interface PaymentCancelMapper {

	PaymentCancelMapper INSTANCE = Mappers.getMapper(PaymentCancelMapper.class);
	
	PaymentCancel toEntity(PaymentCancelDTO dto);

	PaymentCancelDTO toDTO(PaymentCancel entity);

}
