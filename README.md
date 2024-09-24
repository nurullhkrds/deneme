 private final OrderPaymentGroupMapper orderPaymentGroupMapper = OrderPaymentGroupMapper.INSTANCE;

    @Test
    void shouldMapOrderPaymentGroupToDTO() {
        OrderPaymentGroup orderPaymentGroup = new OrderPaymentGroup();
        orderPaymentGroup.setId(1L);
        orderPaymentGroup.setGroupName("Test Group");
        orderPaymentGroup.setActive(true);

        OrderPaymentGroupDTO orderPaymentGroupDTO = orderPaymentGroupMapper.toOrderPaymentGroupDTO(orderPaymentGroup);

        assertNotNull(orderPaymentGroupDTO);
        assertEquals(orderPaymentGroup.getId(), orderPaymentGroupDTO.getId());
        assertEquals(orderPaymentGroup.getGroupName(), orderPaymentGroupDTO.getGroupName());
        assertEquals(orderPaymentGroup.isActive(), orderPaymentGroupDTO.isActive());
    }

    @Test
    void shouldMapDTOToOrderPaymentGroup() {
        OrderPaymentGroupDTO orderPaymentGroupDTO = new OrderPaymentGroupDTO();
        orderPaymentGroupDTO.setId(1L);
        orderPaymentGroupDTO.setGroupName("Test Group DTO");
        orderPaymentGroupDTO.setActive(false);

        OrderPaymentGroup orderPaymentGroup = orderPaymentGroupMapper.toOrderPaymentGroup(orderPaymentGroupDTO);

        assertNotNull(orderPaymentGroup);
        assertEquals(orderPaymentGroupDTO.getId(), orderPaymentGroup.getId());
        assertEquals(orderPaymentGroupDTO.getGroupName(), orderPaymentGroup.getGroupName());
        assertEquals(orderPaymentGroupDTO.isActive(), orderPaymentGroup.isActive());
    }
