
    private final PaymentMethodMapper paymentMethodMapper = PaymentMethodMapper.INSTANCE;

    @Test
    void shouldMapPaymentMethodToDTO() {
        // Given
        PaymentMethod paymentMethod = new PaymentMethod();
        paymentMethod.setId(1L);
        paymentMethod.setMethodName("Credit Card");
        paymentMethod.setEnabled(true);

        // When
        PaymentMethodDTO paymentMethodDTO = paymentMethodMapper.toDTO(paymentMethod);

        // Then
        assertNotNull(paymentMethodDTO);
        assertEquals(paymentMethod.getId(), paymentMethodDTO.getId());
        assertEquals(paymentMethod.getMethodName(), paymentMethodDTO.getMethodName());
        assertEquals(paymentMethod.isEnabled(), paymentMethodDTO.isEnabled());
    }

    @Test
    void shouldMapDTOToPaymentMethod() {
        // Given
        PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();
        paymentMethodDTO.setId(1L);
        paymentMethodDTO.setMethodName("Debit Card");
        paymentMethodDTO.setEnabled(false);

        // When
        PaymentMethod paymentMethod = paymentMethodMapper.toEntity(paymentMethodDTO);

        // Then
        assertNotNull(paymentMethod);
        assertEquals(paymentMethodDTO.getId(), paymentMethod.getId());
        assertEquals(paymentMethodDTO.getMethodName(), paymentMethod.getMethodName());
        assertEquals(paymentMethodDTO.isEnabled(), paymentMethod.isEnabled());
    }
