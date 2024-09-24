@Mapper(componentModel = "spring")
public interface InstitutionProcessMapper {
    InstitutionProcessMapper INSTANCE = Mappers.getMapper(InstitutionProcessMapper.class);

    InstitutionProcessDTO toInstitutionProcessDTO(InstitutionProcess institutionProcess);

    InstitutionProcess toInstitutionProcess(InstitutionProcessDTO institutionProcessDTO);
}

@Mapper(componentModel = "spring")
public interface InstitutionOrderPymMethodMapper {
    InstitutionOrderPymMethodMapper INSTANCE = Mappers.getMapper(InstitutionOrderPymMethodMapper.class);

    InstitutionOrderPymMethodDTO toInstitutionOrderPymMethodDTO(InstitutionOrderPymMethod institutionOrderPymMethod);

    InstitutionOrderPymMethod toInstitutionOrderPymMethod(InstitutionOrderPymMethodDTO institutionOrderPymMethodDTO);
}

@Mapper(componentModel = "spring")
public interface InstitutionMapper {
    InstitutionMapper INSTANCE = Mappers.getMapper(InstitutionMapper.class);

    InstitutionDTO toInstitutionDTO(Institution institution);

    Institution toInstitution(InstitutionDTO institutionDTO);
}

@Mapper(componentModel = "spring")
public interface InstitutionFeatureMapper {
    InstitutionFeatureMapper INSTANCE = Mappers.getMapper(InstitutionFeatureMapper.class);

    InstitutionFeatureDTO toInstitutionFeatureDTO(InstitutionFeature InstitutionFeature);

    InstitutionFeature toInstitutionFeature(InstitutionFeatureDTO institutionFeatureDTO);

	List<InstitutionFeatureDTO> toInstitutionFeatureDTOList(List<InstitutionFeature> findByIsActiveAndInstitutionId);
}


@Mapper(componentModel = "spring")
public interface InstitutionDebtTypeMapper {
    InstitutionDebtTypeMapper INSTANCE = Mappers.getMapper(InstitutionDebtTypeMapper.class);

    InstitutionDebtTypeDTO toDTO(InstitutionDebtType institutionDebtType);

    InstitutionDebtType toEntity(InstitutionDebtTypeDTO institutionDebtTypeDTO);
} 

@Mapper(componentModel = "spring")
public interface InstitutionCommunicationMapper {
    InstitutionCommunicationMapper INSTANCE = Mappers.getMapper(InstitutionCommunicationMapper.class);

    InstitutionCommunicationDTO toInstitutionCommunicationDTO(InstitutionCommunication institutionCommunication);

    InstitutionCommunication toInstitutionCommunication(InstitutionCommunicationDTO institutionCommunicationDTO);
}
