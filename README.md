@Mapper(componentModel = "spring")
public interface ProcessExecutionMapper {


	ProcessExecutionMapper INSTANCE = Mappers.getMapper(ProcessExecutionMapper.class);

	@Mapping(target = "branchCode", source = "operatingBranchCode")
	@Mapping(target = "institutionDebtTypeId", source = "debtTypeID")
	QueryBillProcessInput toQueryBillProcessInput(QueryBillsRequest queryBillsRequest);

	QueryBillsResponse toQueryBillsResponse(QueryBillsProcessOutput queryBillsProcessOutput);
	
	@AfterMapping
	default void afterToGetQueryBillsResponse(@MappingTarget final QueryBillsResponse queryBillsResponse,
			QueryBillsProcessOutput queryBillsProcessOutput) {
		if (CollectionUtils.isEmpty(queryBillsProcessOutput.getProvisionDTOList())) {
			return;
		}
		queryBillsResponse
				.setSubscriberName(queryBillsProcessOutput.getProvisionDTOList().get(0).getSubscriberName());
		queryBillsResponse.setBillList(queryBillsProcessOutput.getProvisionDTOList().stream().map(provisionDTO -> {
			QueriedBillResponseWebDTO queriedBillResponseWebDTO = new QueriedBillResponseWebDTO();
			queriedBillResponseWebDTO.setBillNo(provisionDTO.getBillNo());
			queriedBillResponseWebDTO.setBillAmount(provisionDTO.getAmount());
			queriedBillResponseWebDTO.setBillDueDate(provisionDTO.getBillDueDate());
			queriedBillResponseWebDTO.setCurrency(provisionDTO.getCurrency().getValue());
			queriedBillResponseWebDTO.setBillTerm(provisionDTO.getBillTerm());
			queriedBillResponseWebDTO.setBillProvisionId(provisionDTO.getId().toString());
			queriedBillResponseWebDTO.setExplanation(provisionDTO.getExplanation());
			queriedBillResponseWebDTO.setPayable(provisionDTO.getIsPayable());
			return queriedBillResponseWebDTO;
		}).toList());
	}
	@Mapping(target = "branchCode", source = "operatingBranchCode")
	BillPaymentProcessInput toBillPaymentProcessInput(DoBillPaymentRequest doBillPaymentRequest);

	@Mapping(target = "branchCode", source = "operatingBranchCode")
	BillPaymentReverseProcessInput toBillPaymentReverseProcessInput (CancelBillPaymentRequest cancelBillPaymentRequest);

}
