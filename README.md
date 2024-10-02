	@Query(nativeQuery = true)
	InstitutionPymMethodWebDTO getInstitutionPymMethod(@Param("channelCode") String channelCode, @Param("productCode") String productCode,
			@Param("institutionCode") String institutionCode,
			@Param("institutionDebtTypeId") Long institutionDebtTypeId,@Param("paymentMethod") String paymentMethod);
