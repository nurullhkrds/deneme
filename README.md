  @Test
    void parseSubscriberNoIntoParts_shouldReturnResponse_whenDebtTypeIDIsNull() {
        // Given
        ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
        request.setDebtTypeID(null);
        request.setProductCode("productCode");
        request.setInstitutionCode("institutionCode");
        request.setSubscriberNo("subscriberNo");

        List<InstitutionUserIntfDTO> defaultUserInterface = Collections.singletonList(new InstitutionUserIntfDTO());
        List<SubsrciberNoPartResponseWebDTO> parsedParts = Collections.singletonList(new SubsrciberNoPartResponseWebDTO());

        when(institutionUserIntService.getDefaultUserInterface("productCode", "institutionCode"))
                .thenReturn(defaultUserInterface);
        when(SubscriberNumberUtils.parseSubscriberNoIntoParts(defaultUserInterface, "subscriberNo"))
                .thenReturn(parsedParts);

        // When
        ParseSubscriberNoIntoPartsResponse actualResponse = paymentService.parseSubscriberNoIntoParts(request);

        // Then
        assertNotNull(actualResponse);
        assertEquals(parsedParts, actualResponse.getSubsrciberNoPartResponseWebDTO());
        verify(institutionUserIntService).getDefaultUserInterface("productCode", "institutionCode");
        verifyStatic(SubscriberNumberUtils.class);
        SubscriberNumberUtils.parseSubscriberNoIntoParts(defaultUserInterface, "subscriberNo");
    }

    @Test
    void parseSubscriberNoIntoParts_shouldReturnResponse_whenDebtTypeIDIsNotNull() {
        // Given
        ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
        request.setDebtTypeID(1L);
        request.setSubscriberNo("subscriberNo");

        List<InstitutionUserIntfDTO> userInterface = Collections.singletonList(new InstitutionUserIntfDTO());
        List<SubsrciberNoPartResponseWebDTO> parsedParts = Collections.singletonList(new SubsrciberNoPartResponseWebDTO());

        when(institutionUserIntService.getUserInterface(1L)).thenReturn(userInterface);
        when(SubscriberNumberUtils.parseSubscriberNoIntoParts(userInterface, "subscriberNo"))
                .thenReturn(parsedParts);

        // When
        ParseSubscriberNoIntoPartsResponse actualResponse = paymentService.parseSubscriberNoIntoParts(request);

        // Then
        assertNotNull(actualResponse);
        assertEquals(parsedParts, actualResponse.getSubsrciberNoPartResponseWebDTO());
        verify(institutionUserIntService).getUserInterface(1L);
        verifyStatic(SubscriberNumberUtils.class);
        SubscriberNumberUtils.parseSubscriberNoIntoParts(userInterface, "subscriberNo");
    }
