@Mapper(componentModel = "spring")
public interface InstitutionNetworkInfoMapper {
    InstitutionNetworkInfoMapper INSTANCE = Mappers.getMapper(InstitutionNetworkInfoMapper.class);

    InstitutionNetworkInfoDTO toInstitutionNetworkInfoDTO(InstitutionNetworkInfo institutionNetworkInfo);

    InstitutionNetworkInfo toInstitutionNetworkInfo(InstitutionNetworkInfoDTO institutionNetworkInfoDTO);
}
