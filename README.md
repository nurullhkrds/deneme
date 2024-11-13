@Service
public class AdminInstitutionChnlProccesServiceImpl implements AdminInstitutionChnlProccesService {


    private final InstitutionChannelProcessRepository institutionChannelProcessRepository;
    private final AdminInstitutionChannelProcessMapper institutionChannelProcessMapper;
    private final AdminInstitutionChannelService institutionChannelService;
    private final AdminInstitutionProcessService institutionProcessService;

    public AdminInstitutionChnlProccesServiceImpl(InstitutionChannelProcessRepository institutionChannelProcessRepository, AdminInstitutionChannelProcessMapper institutionChannelProcessMapper, AdminInstitutionChannelService institutionChannelService, AdminInstitutionProcessService institutionProcessService) {
        this.institutionChannelProcessRepository = institutionChannelProcessRepository;
        this.institutionChannelProcessMapper = institutionChannelProcessMapper;
        this.institutionChannelService = institutionChannelService;
        this.institutionProcessService = institutionProcessService;
    }

    @Override
    public List<InstitutionChnlProcessWebDTO> getAllInstitutionChnlProcceses() {
        List<Object []> insChnnlProcessListOjectArray= institutionChannelProcessRepository.findInstitutionChnlProcessWithInstitution();
        return insChnnlProcessListOjectArray.stream()
                .map(institutionChannelProcessMapper::objectArrayToWebDTO)
                .collect(Collectors.toList());
    }

    @Override
    public InstitutionChannelProcessDTO getInstitutionChannelProccesById(Long id) {
        InstitutionChannelProcess institutionChannelProcess= institutionChannelProcessRepository.findById(id).orElse(null);
        if (institutionChannelProcess != null ){
            return institutionChannelProcessMapper.toDTO(institutionChannelProcess);
        }
        return null;
    }

    @Override
    public InstitutionChannelProcessDTO createInstitutionChannelProcces(CreateInstitutionChannelProcessRequestDTO requestDTO) throws MicroException {

        boolean existsByInstitutionChannelAndProcess = institutionChannelProcessRepository
                .existsByInstitutionChannelIdAndInstitutionProcessId(requestDTO.getInstitutionChannelId()
                        , requestDTO.getInstitutionProcessId());
        if (existsByInstitutionChannelAndProcess){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHANNEL_PROCESS);
        }

        InstitutionProcessDTO institutionProcessDTO= institutionProcessService.getInstitutionProcessById(requestDTO.getInstitutionProcessId());

        if (institutionProcessDTO == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_PROCESS_NOT_FOUND);
        }

        InstitutionChannelDTO institutionChannelDTO= institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId());
        if (institutionChannelDTO == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_NOT_FOUND);
        }

        InstitutionChannelProcessDTO dto= institutionChannelProcessMapper.toDTO(requestDTO);
        dto.setInstitutionProcess(institutionProcessDTO);
        dto.setInstitutionChannel(institutionChannelDTO);
        dto.setCreateDate(LocalDateTime.now());

        InstitutionChannelProcess institutionChannelProcess=institutionChannelProcessMapper.toEntity(dto);
        institutionChannelProcess=institutionChannelProcessRepository.save(institutionChannelProcess);
        return institutionChannelProcessMapper.toDTO(institutionChannelProcess);
    }

    @Override
    public InstitutionChannelProcessDTO updateInstitutionChannelProcces(UpdateInstitutionChannelProcessRequestDTO requestDTO) throws MicroException{
        InstitutionChannelProcessDTO institutionChannelProcessDTO= getInstitutionChannelProccesById(requestDTO.getId());
        if (institutionChannelProcessDTO == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_PROCESS_NOT_FOUND);
        }


        boolean existsByInstitutionChannelAndProcess = institutionChannelProcessRepository
                .existsByInstitutionChannelIdAndInstitutionProcessId(requestDTO.getInstitutionChannelId()
                        , requestDTO.getInstitutionProcessId());
        if (existsByInstitutionChannelAndProcess && !institutionChannelProcessDTO.getId().equals(requestDTO.getId())){
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CHANNEL_PROCESS);
        }

        InstitutionProcessDTO institutionProcessDTO= institutionProcessService.getInstitutionProcessById(requestDTO.getInstitutionProcessId());

        if (institutionProcessDTO == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_PROCESS_NOT_FOUND);
        }

        InstitutionChannelDTO institutionChannelDTO= institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId());


        if (institutionChannelDTO == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CHANNEL_NOT_FOUND);
        }

        institutionChannelProcessDTO.setInstitutionChannel(institutionChannelDTO);
        institutionChannelProcessDTO.setInstitutionProcess(institutionProcessDTO);
        institutionChannelProcessDTO.setWorkingStartTime(requestDTO.getWorkingStartTime());
        institutionChannelProcessDTO.setWorkingFinishTime(requestDTO.getWorkingFinishTime());
        institutionChannelProcessDTO.setIsActive(requestDTO.getIsActive());
        institutionChannelProcessDTO.setUpdateDate(LocalDateTime.now());
        institutionChannelProcessDTO.setUpdatedBy(requestDTO.getUpdateUser());

        InstitutionChannelProcess institutionChannelProcess=institutionChannelProcessMapper.toEntity(institutionChannelProcessDTO);
        institutionChannelProcess=institutionChannelProcessRepository.save(institutionChannelProcess);
        return institutionChannelProcessMapper.toDTO(institutionChannelProcess);

    }
}
