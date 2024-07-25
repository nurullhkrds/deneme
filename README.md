@Test
public void testIsFomOperationEnabled_WhenFeatureValueIsYes() {
    // Verileri hazırlayın
    InstitutionDTO institution = new InstitutionDTO();
    ProductDTO productDTO = new ProductDTO();
    productDTO.setCode("123"); // Product kodunu ayarlayın
    institution.setProduct(productDTO); // ProductDTO'yu institution'a ekleyin
    institution.setInstitutionCode("123"); // Institution kodunu ayarlayın
    institution.getProduct().setFomCheckFlag(false); // FomCheckFlag'ı false olarak ayarlayın

    // Mock davranışını ayarlayın
    when(institutionFeatureService.getFeatureValue(EnumFeatureCode.FOM_CHECK_ENABLED, institution, productDTO))
            .thenReturn(EnumYesNo.YES.getValue());

    // Metodu test edin
    boolean result = paymentUtilImpl.isFomOperationEnabled(institution);

    // Sonuç doğrulama
    assertTrue(result, "Fom operation should be enabled when feature value is 'YES'");
}
