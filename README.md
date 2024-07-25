    @Test
    public void testIsFomOperationEnabled_WhenFeatureValueIsYes() {
        InstitutionDTO institution = new InstitutionDTO();
        ProductDTO productDTO=new ProductDTO();
        productDTO.setCode("123");
        institution.setProduct(new ProductDTO());
        institution.setInstitutionCode("123");
        institution.getProduct().setFomCheckFlag(false);

        when(institutionFeatureService.getFeatureValue(EnumFeatureCode.FOM_CHECK_ENABLED, institution.getInstitutionCode(), productDTO.getCode()))
                .thenReturn(EnumYesNo.YES.getValue());

        boolean result = paymentUtilImpl.isFomOperationEnabled(institution);

        assertTrue(result, "mesaj");
    }org.opentest4j.AssertionFailedError: mesaj ==> 
Expected :true
Actual   :false
