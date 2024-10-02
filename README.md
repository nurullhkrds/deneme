@Mapper
public interface InstitutionChnnlPymMthdAccMapper {
    
    @Mapping(target = "institutionChannelPymMethod", source = "institutionChannelPymMethod")
    InstitutionChnnlPymMthdAccDTO toDTO(Object[] row);
}
@Query(value = "SELECT icpa.*, icpm.* " +
               "FROM institution_chnnl_pym_mthd_acc icpa " +
               "JOIN institution_channel_pym_method icpm ON icpa.inst_channel_pym_method_id = icpm.id", 
       nativeQuery = true)
List<Object[]> findAllInstitutionChnnlPymMthdAccWithRelations();
