public interface InstitutionRepository extends JpaRepository<Institution, Long> {

    @Query(value = "SELECT CASE WHEN COUNT(*) > 0 THEN TRUE ELSE FALSE END " +
                   "FROM INSTITUTION " +
                   "WHERE PRODUCT_CODE = :productCode AND INSTITUTION_CODE = :institutionCode", nativeQuery = true)
    boolean existsByProductCodeAndInstitutionCode(@Param("productCode") String productCode,
                                                  @Param("institutionCode") String institutionCode);
}

  boolean exists = institutionRepository.existsByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
    if (exists) {
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);  
        error.setErrorMessage("Product code and institution code combination already exists.");
        error.setApplicationName(SERVICE_NAME);
        throw new DataConflictException(error);
    }
