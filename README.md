
    @Test
    void insertPayment_shouldReturnPaymentDTO_whenPaymentEntityIsSavedSuccessfully() {
        // Given
        PaymentDTO paymentDTO = new PaymentDTO();
        Payment paymentEntity = new Payment();
        Payment savedPaymentEntity = new Payment();

        when(paymentMapper.toEntity(paymentDTO)).thenReturn(paymentEntity);
        when(paymentRepository.save(paymentEntity)).thenReturn(savedPaymentEntity);
        when(paymentMapper.toDTO(savedPaymentEntity)).thenReturn(paymentDTO);

        // When
        PaymentDTO actualPaymentDTO = paymentService.insertPayment(paymentDTO);

        // Then
        assertEquals(paymentDTO, actualPaymentDTO);
        verify(paymentMapper).toEntity(paymentDTO);
        verify(paymentRepository).save(paymentEntity);
        verify(paymentMapper).toDTO(savedPaymentEntity);
    }

    @Test
    void getPayment_shouldReturnPaymentDTO_whenPaymentExists() {
        // Given
        Long id = 1L;
        Long contractNo = 123L;
        PaymentDTO paymentDTO = new PaymentDTO();
        Payment paymentEntity = new Payment();

        when(paymentRepository.findByIdAndContractNo(id, contractNo)).thenReturn(Optional.of(paymentEntity));
        when(paymentMapper.toDTO(paymentEntity)).thenReturn(paymentDTO);

        // When
        PaymentDTO actualPaymentDTO = paymentService.getPayment(id, contractNo);

        // Then
        assertEquals(paymentDTO, actualPaymentDTO);
        verify(paymentRepository).findByIdAndContractNo(id, contractNo);
        verify(paymentMapper).toDTO(paymentEntity);
    }

    @Test
    void getPayment_shouldReturnNull_whenPaymentDoesNotExist() {
        // Given
        Long id = 1L;
        Long contractNo = 123L;

        when(paymentRepository.findByIdAndContractNo(id, contractNo)).thenReturn(Optional.empty());

        // When
        PaymentDTO actualPaymentDTO = paymentService.getPayment(id, contractNo);

        // Then
        assertNull(actualPaymentDTO);
        verify(paymentRepository).findByIdAndContractNo(id, contractNo);
    }

    @Test
    void updateStatus_shouldUpdatePaymentStatusSuccessfully() {
        // Given
        EnumBillStatu status = EnumBillStatu.PAID;
        Long id = 1L;
        Long contractNo = 123L;

        // When
        paymentService.updateStatus(status, id, contractNo);

        // Then
        verify(paymentRepository).updateStatus(status.getValue(), id, contractNo);
    }

    @Test
    void insertPaymentCancel_shouldReturnPaymentCancelDTO_whenPaymentCancelEntityIsSavedSuccessfully() {
        // Given
        PaymentCancelDTO paymentCancelDTO = new PaymentCancelDTO();
        PaymentCancel paymentCancelEntity = new PaymentCancel();
        PaymentCancel savedPaymentCancelEntity = new PaymentCancel();

        when(paymentCancelMapper.toEntity(paymentCancelDTO)).thenReturn(paymentCancelEntity);
        when(paymentCancelRepository.save(paymentCancelEntity)).thenReturn(savedPaymentCancelEntity);
        when(paymentCancelMapper.toDTO(savedPaymentCancelEntity)).thenReturn(paymentCancelDTO);

        // When
        PaymentCancelDTO actualPaymentCancelDTO = paymentService.insertPaymentCancel(paymentCancelDTO);

        // Then
        assertEquals(paymentCancelDTO, actualPaymentCancelDTO);
        verify(paymentCancelMapper).toEntity(paymentCancelDTO);
        verify(paymentCancelRepository).save(paymentCancelEntity);
        verify(paymentCancelMapper).toDTO(savedPaymentCancelEntity);
    }
