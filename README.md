   @Test
    void findPaymentByIdWithLock_shouldReturnPayment_whenPaymentIdIsNotNull() {
        // Given
        Long paymentId = 1L;
        Payment payment = new Payment();
        when(paymentRepository.findByIdWithLock(paymentId)).thenReturn(Optional.of(payment));

        // When
        Payment result = paymentService.findPaymentByIdWithLock(paymentId);

        // Then
        assertNotNull(result);
        assertEquals(payment, result);
        verify(paymentRepository).findByIdWithLock(paymentId);
    }

    @Test
    void findPaymentByIdWithLock_shouldReturnNull_whenPaymentIdIsNull() {
        // When
        Payment result = paymentService.findPaymentByIdWithLock(null);

        // Then
        assertNull(result);
        verify(paymentRepository, never()).findByIdWithLock(anyLong());
    }

  @Test
    void updatePayment_shouldSavePayment() {
        // Given
        Payment payment = new Payment();

        // When
        paymentService.updatePayment(payment);

        // Then
        verify(paymentRepository).save(payment);
    }

    @Test
    void getPayment_shouldReturnPaymentDTO_whenPaymentIdIsNotNull() {
        // Given
        Long paymentId = 1L;
        Payment payment = new Payment();
        PaymentDTO paymentDTO = new PaymentDTO();
        when(paymentRepository.findById(paymentId)).thenReturn(Optional.of(payment));
        when(paymentMapper.toDTO(payment)).thenReturn(paymentDTO);

        // When
        PaymentDTO result = paymentService.getPayment(paymentId);

        // Then
        assertNotNull(result);
        assertEquals(paymentDTO, result);
        verify(paymentRepository).findById(paymentId);
    }

    @Test
    void getPayment_shouldReturnNull_whenPaymentIdIsNull() {
        // When
        PaymentDTO result = paymentService.getPayment(null);

        // Then
        assertNull(result);
        verify(paymentRepository, never()).findById(anyLong());
    }
 @Test
    @Transactional
    void notifyPayment_shouldReturnNotifyPaymentResponse() throws MicroException {
        // Given
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        NotifyPaymentProcessOutput processOutput = new NotifyPaymentProcessOutput();
        NotifyPaymentResponse notifyPaymentResponse = new NotifyPaymentResponse();
        when(processManager.executeProcess(any(NotifyPaymentProcessInput.class)))
                .thenReturn(processOutput);
        when(paymentMapper.toNotifyPaymentResponse(processOutput)).thenReturn(notifyPaymentResponse);

        // When
        NotifyPaymentResponse result = paymentService.notifyPayment(event);

        // Then
        assertNotNull(result);
        assertEquals(notifyPaymentResponse, result);
        verify(processManager).executeProcess(any(NotifyPaymentProcessInput.class));
        verify(paymentMapper).toNotifyPaymentResponse(processOutput);
    }

    @Test
    void getPaymentCancel_shouldReturnPaymentCancelDTO_whenPaymentIdIsNotNull() {
        // Given
        Long paymentId = 1L;
        PaymentCancel paymentCancel = new PaymentCancel();
        PaymentCancelDTO paymentCancelDTO = new PaymentCancelDTO();
        when(paymentCancelRepository.findByPaymentId(paymentId)).thenReturn(Optional.of(paymentCancel));
        when(paymentCancelMapper.toDTO(paymentCancel)).thenReturn(paymentCancelDTO);

        // When
        PaymentCancelDTO result = paymentService.getPaymentCancel(paymentId);

        // Then
        assertNotNull(result);
        assertEquals(paymentCancelDTO, result);
        verify(paymentCancelRepository).findByPaymentId(paymentId);
    }

    @Test
    void getPaymentCancel_shouldReturnNull_whenPaymentIdIsNull() {
        // When
        PaymentCancelDTO result = paymentService.getPaymentCancel(null);

        // Then
        assertNull(result);
        verify(paymentCancelRepository, never()).findByPaymentId(anyLong());
    }
   @Test
    @Transactional
    void notifyPaymentCancel_shouldReturnNotifyPaymentCancelResponse() throws MicroException {
        // Given
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        NotifyPaymentCancelProcessOutput processOutput = new NotifyPaymentCancelProcessOutput();
        NotifyPaymentCancelResponse notifyPaymentCancelResponse = new NotifyPaymentCancelResponse();
        when(processManager.executeProcess(any(NotifyPaymentCancelProcessInput.class)))
                .thenReturn(processOutput);
        when(paymentMapper.toNotifyPaymentCancelResponse(processOutput)).thenReturn(notifyPaymentCancelResponse);

        // When
        NotifyPaymentCancelResponse result = paymentService.notifyPaymentCancel(event);

        // Then
        assertNotNull(result);
        assertEquals(notifyPaymentCancelResponse, result);
        verify(processManager).executeProcess(any(NotifyPaymentCancelProcessInput.class));
        verify(paymentMapper).toNotifyPaymentCancelResponse(processOutput);
    }
   @Test
    void getMicroBillList_shouldReturnHmnPaidBillDTOList() throws MicroException {
        // Given
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        List<PaidBillResponseWebDTO> paidBillResponseWebDTOList = List.of(new PaidBillResponseWebDTO());
        List<HmnPaidBillDTO> hmnPaidBillDTOList = List.of(new HmnPaidBillDTO());
        when(paymentMapper.toHmnPaidBillDTOList(paidBillResponseWebDTOList)).thenReturn(hmnPaidBillDTOList);

        // When
        List<HmnPaidBillDTO> result = paymentService.getMicroBillList(request);

        // Then
        assertNotNull(result);
        assertEquals(hmnPaidBillDTOList, result);
        verify(paymentMapper).toHmnPaidBillDTOList(paidBillResponseWebDTOList);
    }
 @Test
    void getReconCount_shouldReturnCountDTO() throws MicroException {
        // Given
        boolean isPayment = true;
        Date reconciliationDate = new Date();
        String productCode = "productCode";
        String institutionCode = "institutionCode";
        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setId(1L);
        CountDTO countDTO = new CountDTO();
        when(institutionService.getInstitution(productCode, institutionCode)).thenReturn(institutionDTO);
        when(paymentRepository.getReconCount(institutionDTO.getId(), reconciliationDate, EnumBillStatu.PAID.getValue()))
                .thenReturn(countDTO);

        // When
        CountDTO result = paymentService.getReconCount(isPayment, reconciliationDate, productCode, institutionCode);

        // Then
        assertNotNull(result);
        assertEquals(countDTO, result);
        verify(institutionService).getInstitution(productCode, institutionCode);
        verify(paymentRepository).getReconCount(institutionDTO.getId(), reconciliationDate, EnumBillStatu.PAID.getValue());
    }
 @Test
    void getReconDetail_shouldReturnHmnRecoDetailDTOList() throws MicroException {
        // Given
        boolean isPayment = true;
        Date reconciliationDate = new Date();
        String productCode = "productCode";
        String institutionCode = "institutionCode";
        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setId(1L);
        List<Payment> payments = List.of(new Payment());
        List<HmnPaidBillDTO> hmnRecoDetailDTOList = List.of(new HmnPaidBillDTO());
        when(institutionService.getInstitution(productCode, institutionCode)).thenReturn(institutionDTO);
        when(paymentRepository.getReconDetail(institutionDTO.getId(), reconciliationDate, EnumBillStatu.PAID.getValue()))
                .thenReturn(payments);
        when(paymentMapper.toHmnRecoDetailDTOList(payments)).thenReturn(hmnRecoDetailDTOList);

        // When
        List<HmnPaidBillDTO> result = paymentService.getReconDetail(isPayment, reconciliationDate, productCode, institutionCode);

        // Then
        assertNotNull(result);
        assertEquals(hmnRecoDetailDTOList, result);
        verify(institutionService).getInstitution(productCode, institutionCode);
        verify(paymentRepository).getReconDetail(institutionDTO.getId(), reconciliationDate, EnumBillStatu.PAID.getValue());
        verify(paymentMapper).toHmnRecoDetailDTOList(payments);
    }
