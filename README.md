INST_CHANNEL_PYM_METHOD_ID, CURRENCY


INST_CHANNEL_PYM_METHOD_ID, COLLECTION_ACCOUNT_NO

@Getter
@Setter
public class CreateInstitutionChnlPymMthdAccRequestDTO extends BaseCreateRequestDTO {

    private Long id;

    private Long institutionChannelPymMethodId;


    private String collectionAccountNo;


    private String institutionAccountNo;


    private String currency;


    private EnumExpenseType expenseType;


    private String expenseAccountNo;

    private Boolean isActive;
}


  @Override
    public InstitutionChnnlPymMthdAccDTO createInstitutionChannelPymMethodAcc(CreateInstitutionChnlPymMthdAccRequestDTO requestDTO) throws MicroException {


        return null;
    }
