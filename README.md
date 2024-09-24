@Mapper(componentModel = "spring")
public interface MerchantInformationMapper {
    MerchantInformationMapper INSTANCE = Mappers.getMapper(MerchantInformationMapper.class);

    MerchantInformationDTO toMerchantInformationDTO(MerchantInformation merchantInformation);

    MerchantInformation toMerchantInformation(MerchantInformationDTO merchantInformationDTO);
}


@Mapper(componentModel = "spring")
public interface InstUserIntfSubtypeMapper {
    InstUserIntfSubtypeMapper INSTANCE = Mappers.getMapper(InstUserIntfSubtypeMapper.class);

    InstUserIntfSubtypeDTO toInstUserIntfSubtypeDTO(InstUserIntfSubtype IinstUserIntfSubtype);

    InstUserIntfSubtype toInstUserIntfSubtype(InstUserIntfSubtypeDTO instUserIntfSubtypeDTO);
}

@Mapper(componentModel = "spring")
public interface InstitutionUserIntfMapper {
    InstitutionUserIntfMapper INSTANCE = Mappers.getMapper(InstitutionUserIntfMapper.class);

    InstitutionUserIntfDTO toInstitutionUserIntfDTO(InstitutionUserIntf institutionUserIntf);

    InstitutionUserIntf toInstitutionUserIntf(InstitutionUserIntfDTO institutionUserIntfDTO);

    List<InstitutionUserIntf> toEntityList(List<InstitutionUserIntfDTO> dtoList);

    List<InstitutionUserIntfDTO> toDTOList(List<InstitutionUserIntf> entityList);
}

@Mapper(componentModel = "spring")
public interface InstitutionPymMethodMapper {
    InstitutionPymMethodMapper INSTANCE = Mappers.getMapper(InstitutionPymMethodMapper.class);

    InstitutionPymMethodDTO toInstitutionPymMethodDTO(InstitutionPymMethod institutionPymMethod);

    InstitutionPymMethod toInstitutionPymMethod(InstitutionPymMethodDTO institutionPymMethodDTO);
}
