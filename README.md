@Service
@RequiredArgsConstructor
public class InstitutionChnnlPymMthdAccServiceImpl implements  InstitutionChnnlPymMthdAccService{
    private final InstitutionChnnlPymMthdAccRepository institutionChnnlPymMthdAccRepository;
    private final InstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper;
    @Override
    public InstitutionChnnlPymMthdAccDTO getInstitutionChnnlPymMthdAcc(Long institutionChannelPymMethodId, String currency) {
        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = institutionChnnlPymMthdAccRepository.findFirstByInstitutionChannelPymMethodAndCurrency(institutionChannelPymMethodId,currency);
        return institutionChnnlPymMthdAccMapper.toDTO(institutionChnnlPymMthdAcc);
    }
}
