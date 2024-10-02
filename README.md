@Query("SELECT new com.example.dto.InstitutionChnnlPymMthdAccDTO(i.id, i.collectionAccountNo, i.currency, i.institutionChannelPymMethod.id) FROM InstitutionChnnlPymMthdAcc i")
List<InstitutionChnnlPymMthdAccDTO> findAllInstitutionChnnlPymMthdAccDTO();
