@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionChnnlPymMthdPscDTO extends UpdatableBaseDTO {

	private Long id;
	private InstitutionChannelPymMethodDTO institutionChannelPymMethod;
	private Integer mondayBlockDayCount;
	private Integer tuesdayBlockDayCount;
	private Integer wednesdayBlockDayCount;
	private Integer thursdayBlockDayCount;
	private Integer fridayBlockDayCount;
	private Integer saturdayBlockDayCount;
	private Integer sundayBlockDayCount;

	public Integer getBlockDayCount(Integer weekendDay){
		switch (weekendDay){
			case 2: return mondayBlockDayCount;
			case 3: return tuesdayBlockDayCount;
			case 4: return wednesdayBlockDayCount;
			case 5: return thursdayBlockDayCount;
			case 6: return fridayBlockDayCount;
			case 7: return saturdayBlockDayCount;
			case 1: return sundayBlockDayCount;
			default: return 0;
		}
	}
}
