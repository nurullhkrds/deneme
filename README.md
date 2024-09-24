@Mapper(componentModel = "spring")
public interface InstitutionCityMapper {
    InstitutionCityMapper INSTANCE = Mappers.getMapper(InstitutionCityMapper.class);
    
    InstitutionCityDTO toInstitutionCityDTO(InstitutionCity institutionCity);

    InstitutionCity toInstitutionCity(InstitutionCityDTO IinstitutionCityDTO);
}


----------------

@Mapper(componentModel = "spring")
public interface InstitutionChnnlPymMthdPscMapper {
    InstitutionChnnlPymMthdPscMapper INSTANCE = Mappers.getMapper(InstitutionChnnlPymMthdPscMapper.class);

    InstitutionChnnlPymMthdPscDTO toInstitutionChnnlPymMthdPscDTO(InstitutionChnnlPymMthdPsc institutionChnnlPymMthdPsc);

    InstitutionChnnlPymMthdPsc toInstitutionChnnlPymMthdPsc(InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO);
}
----

@Mapper(componentModel = "spring")
public interface InstitutionChnnlPymMthdAccMapper {
    InstitutionChnnlPymMthdAccMapper INSTANCE = Mappers.getMapper(InstitutionChnnlPymMthdAccMapper.class);

    InstitutionChnnlPymMthdAccDTO toDTO(InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc);

    InstitutionChnnlPymMthdAcc toInstitutionChnnlPymMthdAcc(InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO);
}
-----
@Mapper(componentModel = "spring")
public interface InstitutionChannelPymMethodMapper {
    InstitutionChannelPymMethodMapper INSTANCE = Mappers.getMapper(InstitutionChannelPymMethodMapper.class);

    InstitutionChannelPymMethodDTO toDTO(InstitutionChannelPymMethod institutionChannelPymMethod);
     
    InstitutionChannelPymMethod toEntity(InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO);
}
----------

@Mapper(componentModel = "spring")
public interface InstitutionChannelProcessMapper {
    InstitutionChannelProcessMapper INSTANCE = Mappers.getMapper(InstitutionChannelProcessMapper.class);

    InstitutionChannelProcessDTO toInstitutionChannelProcessDTO(InstitutionChannelProcess institutionChannelProcess);
      
    InstitutionChannelProcess toInstitutionChannelProcess(InstitutionChannelProcessDTO institutionChannelProcessDTO);
}
