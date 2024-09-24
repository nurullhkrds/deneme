@Mapper(componentModel = "spring")
public interface OrderPaymentGroupMapper {
    OrderPaymentGroupMapper INSTANCE = Mappers.getMapper(OrderPaymentGroupMapper.class);

    OrderPaymentGroupDTO toOrderPaymentGroupDTO(OrderPaymentGroup orderPaymentGroup);

    OrderPaymentGroup toOrderPaymentGroup(OrderPaymentGroupDTO orderPaymentGroupDTO);
}
