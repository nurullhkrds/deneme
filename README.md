    @Test
    public void testParseSubscriberNoIntoParts_withDebtTypeID() {
        // Arrange
        ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
        request.setDebtTypeID(1L);
        request.setProductCode("productCode");
        request.setInstitutionCode("institutionCode");
        request.setSubscriberNo("subscriberNo");

        List<InstitutionUserIntfDTO> institutionUserIntfDTOList = new ArrayList<>();
        List<SubsrciberNoPartResponseWebDTO> subscriberNoIntoPartList = new ArrayList<>();

        when(institutionUserIntService.getUserInterface(request.getDebtTypeID())).thenReturn(institutionUserIntfDTOList);
        // Statik metot çağrısını doğrulamak için gerçek çağrıyı yapıyoruz
        subscriberNoIntoPartList = SubscriberNumberUtils.parseSubscriberNoIntoParts(institutionUserIntfDTOList, request.getSubscriberNo());

        // Act
        ParseSubscriberNoIntoPartsResponse response = billService.parseSubscriberNoIntoParts(request);

        // Assert
        assertNotNull(response);
        assertEquals(subscriberNoIntoPartList, response.getSubsrciberNoPartResponseWebDTO());
    }

    @Test
    public void testParseSubscriberNoIntoParts_withoutDebtTypeID() {
        // Arrange
        ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
        request.setDebtTypeID(null);
        request.setProductCode("productCode");
        request.setInstitutionCode("institutionCode");
        request.setSubscriberNo("subscriberNo");

        List<InstitutionUserIntfDTO> institutionUserIntfDTOList = new ArrayList<>();
        List<SubsrciberNoPartResponseWebDTO> subscriberNoIntoPartList = new ArrayList<>();

        when(institutionUserIntService.getDefaultUserInterface(request.getProductCode(), request.getInstitutionCode())).thenReturn(institutionUserIntfDTOList);
        // Statik metot çağrısını doğrulamak için gerçek çağrıyı yapıyoruz
        subscriberNoIntoPartList = SubscriberNumberUtils.parseSubscriberNoIntoParts(institutionUserIntfDTOList, request.getSubscriberNo());

        // Act
        ParseSubscriberNoIntoPartsResponse response = billService.parseSubscriberNoIntoParts(request);

        // Assert
        assertNotNull(response);
        assertEquals(subscriberNoIntoPartList, response.getSubsrciberNoPartResponseWebDTO());
    }
