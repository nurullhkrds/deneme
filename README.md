@Mapper(componentModel = "spring")
public interface ChannelMapper {
    ChannelMapper INSTANCE = Mappers.getMapper(ChannelMapper.class);

    ChannelDTO toDto(Channel channel);
    
    Channel toEntity(ChannelDTO channelDTO); 
}



@Mapper(componentModel = "spring")
public interface CityMapper {
    CityMapper INSTANCE = Mappers.getMapper(CityMapper.class);

    CityDTO toCityDTO(City city);
  
    City toCity(CityDTO cityDTO);
}


@Mapper(componentModel = "spring")
public interface FeatureMapper {
    FeatureMapper INSTANCE = Mappers.getMapper(FeatureMapper.class);

    FeatureDTO toFeatureDTO(Feature feature);
    
    Feature toFeature(FeatureDTO entity);
}



@Mapper(componentModel = "spring")
public interface InstitutionAccountingInfoMapper {
    InstitutionAccountingInfoMapper INSTANCE = Mappers.getMapper(InstitutionAccountingInfoMapper.class);

    InstitutionAccountingInfoDTO toInstitutionAccountingInfoDTO(InstitutionAccountingInfo institutionAccountingInfo);

    InstitutionAccountingInfo toInstitutionAccountingInfo(InstitutionAccountingInfoDTO institutionAccountingInfoDTO);
}


@Mapper(componentModel = "spring")
public interface InstitutionChannelMapper {
    InstitutionChannelMapper INSTANCE = Mappers.getMapper(InstitutionChannelMapper.class);

    InstitutionChannelDTO toDTO(InstitutionChannel institutionChannel);

    InstitutionChannel toEntity(InstitutionChannelDTO institutionChannelDTO);

}
