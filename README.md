institution_chnnl_pym_mthd_acc tablosununda ID,INST_CHANNEL_PYM_METHOD_ID ,COLLECTION_ACCOUNT_NO,INSTITUTION_ACCOUNT_NO,CURRENCY,EXPENSE_TYPE,EXPENSE_ACCOUNT_NO,IS_ACTIVE,CREATE_DATE,UPDATE_DATE,UPDATED_BY


BU KOLONLAR VARDIR INST_CHANNEL_PYM_METHOD_ID BU BİR FOREİGN KEYDİR.



@Getter
@Setter
public class InstitutionChnlPymMthdAccWebDTO {

    private Long id;
    private Long institutionChannelPymMethodId;
    private String collectionAccountNo;
    private String institutionAccountNo;
    private String currency;
    private EnumExpenseType expenseType;
    private String expenseAccountNo;
    private Boolean isActive;

}
