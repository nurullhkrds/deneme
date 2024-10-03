@Getter
@Setter
public class CreateInstitutionChnlPymMthdPscRequest extends BaseCreateWebRequest {


    @NotNull
    @Schema(description = "ID of the institution channel pym method", example = "52145", required = true)
    private Long institutionChannelPymMethodId;



    @NotNull
    @Schema(description = "Monday block day count", example = "1", required = true)
    private Integer mondayBlockDayCount;

    @NotNull
    private Integer tuesdayBlockDayCount;

    private Integer wednesdayBlockDayCount;

    private Integer thursdayBlockDayCount;

    private Integer fridayBlockDayCount;

    private Integer saturdayBlockDayCount;

    private Integer sundayBlockDayCount;
}
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer mondayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer tuesdayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer wednesdayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer thursdayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer fridayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer saturdayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer sundayBlockDayCount;
