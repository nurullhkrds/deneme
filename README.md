@Mapper(componentModel = "spring")
public interface PaymentMethodMapper {
    PaymentMethodMapper INSTANCE = Mappers.getMapper(PaymentMethodMapper.class);

    PaymentMethodDTO toDTO(PaymentMethod paymentMethod);

    PaymentMethod toEntity(PaymentMethodDTO paymentMethodDTO);
}
