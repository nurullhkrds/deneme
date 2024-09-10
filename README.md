our branch is up to date with 'origin/feature/dev_nk_20241908_return_map_definition'.

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Changes to be committed:
        modified:   pom.xml
        modified:   src/main/java/com/ykb/payments/bill/transaction/adapter/criteria/ReturnMapCriteria.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/adapter/domain/ReturnMap.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/adapter/domain/ReturnMapDefinition.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/adapter/dto/ReturnMapDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/adapter/dto/ReturnMapDefinitionDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/adapter/enums/EnumReturnType.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/common/constant/AccountingConstant.java
        new file:   src/main/java/com/ykb/payments/bill/transaction/common/exception/BillTransactionException.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/common/util/CommonUtils.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/external/adapter/request/CancelledBillAdapterDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/external/adapter/request/NotifyPaymentCancelAdapterRequest.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/external/adapter/request/PaidBillAdapterDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/external/adapter/response/NotifiedBillAdapterDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/external/adapter/response/NotifiedCancelledBillAdapterDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/external/cardpayment/service/SwtSwitchIntegrationServiceImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/external/corebanking/account/client/ProvisionNextClient.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/external/harmoni/billpayment/rest/client/BillPaymentRestFacadeClient.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/Channel.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/City.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/Feature.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/InstitutionChannelPymMethod.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/InstitutionDebtType.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/InstitutionProcess.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/InstitutionUserIntf.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/MerchantInformation.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/OrderPaymentGroup.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/OwnerDepartment.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/PaymentMethod.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/Process.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/domain/ProductFamily.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/dto/ChannelDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/dto/FeatureDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/dto/InstitutionChannelPymMethodDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/dto/InstitutionProcessDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/dto/MerchantInformationDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/dto/OrderPaymentGroupDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/dto/PaymentMethodDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/dto/ProcessDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/repository/InstitutionDebtTypeRepository.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/service/ChannelServiceImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/service/InstitutionDetailServiceImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/service/InstitutionPaymentMethodServiceImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/service/InstitutionService.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/util/SubscriberNumberUtils.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/InstitutionAdkController.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/response/CityWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/response/GetInstitutionDetailListResponse.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/response/GetProductWithCityListResponse.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/response/InstUserIntfSubtypeWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/response/InstitutionDebtTypeWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/response/InstitutionDetailListWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/response/InstitutionUserIntfWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/institution/web/response/ProductCityWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/consumer/BillEventConsumer.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/facade/PaymentFacadeImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/mapper/HarmoniMicroMapper.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/model/CommissionServiceRequestDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/model/PaymentNotificationEvent.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/paymentnotification/web/PaymentNotificationController.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/producer/PaymentNotificationEventProducer.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/service/InstitutionBarcodeServiceImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/service/PaymentCommissionService.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/service/PaymentCommissionServiceImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/service/PaymentNotificationServiceImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/service/PaymentServiceImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/service/PaymentUtilImpl.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/web/HarmoniPaymentAdkController.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/web/request/AccountPaymentMethodDetailWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/web/request/CashPaymentMethodDetailWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/web/request/CreditCardPaymentMethodDetailWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/web/request/HmnTellerTransactionDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/web/response/GetCustomerPaidBillListResponse.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/payment/web/response/QueriedBillResponseWebDTO.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/process/common/AbstractProcess.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/process/common/ProcessStep.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/process/notifypayment/NotifyPaymentProcess.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/process/payment/BillPaymentReverseProcess.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/process/query/QueryBillsProcess.java
        modified:   src/main/java/com/ykb/payments/bill/transaction/process/query/QueryBillsProcessOutput.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/accounting/provision/service/AccountReverseProvisionServiceImplTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/accounting/provision/service/AccountingProvisionServiceImplTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/accounting/provision/service/ProvisionFactoryTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/accounting/provision/service/ReverseProvisionFactoryTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/accounting/receipt/enums/EnumPymBillTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/accounting/receipt/enums/EnumReceiptSourceTypeTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/accounting/util/AccountingUtilTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/adapter/enums/EnumReturnTypeTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/adapter/enums/converter/EnumReturnTypeConverterTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/adapter/web/ReturnMapControllerTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/common/constant/BillTransactionConstantTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/common/domain/BaseTransactionalEntityListenerTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/common/util/CommonUtilsTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/common/util/DateUtilsTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/config/BillTransactionConfigTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/config/RedisConfigTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/external/cardpayment/SwtSwitchIntegrationServiceImplTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/external/corebanking/data/lookup/service/AccountingDataLookupServiceImplTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/external/harmoni/billpayment/rest/service/BillPaymentRestFacadeImplTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/institution/comporator/ProductComparatorTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/institution/enums/EnumOrderPaymentGroupTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/institution/service/ProcessChannelServiceImplTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/institution/util/SubscriberNumberUtilsTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/payment/enums/EnumPaymentNotificationStatuConverterTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/payment/enums/EnumPaymentNotificationTypeConverterTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/payment/event/PaymentEventListenerTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/payment/event/PaymentEventPublisherTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/payment/facade/PaymentFacadeImplTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/payment/mapper/HarmoniMicroMapperTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/payment/mapper/MicroHarmoniMapperTest.java
        modified:   src/test/java/com/ykb/payments/bill/transaction/payment/mapper/PaymentMapperTest.java
Unmerged paths:
  (use "git add/rm <file>..." as appropriate to mark resolution)
        deleted by us:   src/test/java/com/ykb/payments/bill/transaction/external/harmoni/billpayment/rest/client/BillPaymentRestFacadeClientTest.java

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hs_err_pid16100.log
