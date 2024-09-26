java: Unknown property "institutionChannelId" in result type InstitutionChannelWebDTO. Did you mean "institutionId"?



    @Mapping(target = "institutionChannelId", source = "channel.code")
    @Mapping(target = "institutionId", source = "institutionDebtType.institution.id")
    @Mapping(target = "institutionName", source = "institutionDebtType.institution.name")
    InstitutionChannelWebDTO toWebDTO (InstitutionChannelDTO dto);


@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class InstitutionChannelWebDTO {

    private Long id;

    private Long institutionId;

    private String institutionName;

    private String channelCode;

}

@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionChannelDTO extends UpdatableBaseDTO {

	private Long id;	
	private InstitutionDebtTypeDTO institutionDebtType;
	private ChannelDTO channel;
	private Boolean isNewBillNeeded;
	private Boolean isPartialPaymentAllowed;
	private Boolean isOverPaymentAllowed;
	private LocalTime workingStartTime;
	private LocalTime workingFinishTime;
	private Boolean isActive;

}
