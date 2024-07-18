7436 ms
Hibernate: select returnmap0_.id as id1_59_, returnmap0_.create_date as create_date2_59_, returnmap0_.created_by as created_by3_59_, returnmap0_.update_date as update_date4_59_, returnmap0_.updated_by as updated_by5_59_, returnmap0_.version as version6_59_, returnmap0_.bank_return_code as bank_return_code7_59_, returnmap0_.bank_return_text as bank_return_text8_59_, returnmap0_.institution_return_code as institution_return9_59_, returnmap0_.institution_return_text as institution_retur10_59_, returnmap0_.is_reversible as is_reversible11_59_, returnmap0_.return_map_code as return_map_code12_59_, returnmap0_.return_type as return_type13_59_ from return_map returnmap0_ where returnmap0_.return_map_code=?
2024-07-18 13:25:42,412 ERROR [http-nio-8080-exec-10][PymExceptionHandler] An Exception occured java.lang.ClassCastException: class java.util.ArrayList cannot be cast to class com.ykb.payments.bill.transaction.adapter.core.DataResult (java.util.ArrayList is in module java.base of loader 'bootstrap'; com.ykb.payments.bill.transaction.adapter.core.DataResult is in unnamed module of loader 'app')
	at com.ykb.payments.bill.transaction.adapter.services.concretes.ReturnMapService.getAllReturnMapList(ReturnMapService.java:26)
	at com.ykb.payments.bill.transaction.adapter.web.ReturnMapController.getAllReturnMapList(ReturnMapController.java:28)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:150)


{
  "exceptionData": {
    "applicationName": "PAYMENTS.BILL.bill-transaction",
    "errorCode": -999,
    "errorMessage": "class java.util.ArrayList cannot be cast to class com.ykb.payments.bill.transaction.adapter.core.DataResult (java.util.ArrayList is in module java.base of loader 'bootstrap'; com.ykb.payments.bill.transaction.adapter.core.DataResult is in unnamed module of loader 'app')",
    "traceId": null
  },
  "parameters": {}
}
  @GetMapping("/getAllReturnMapList")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> getAllReturnMapList(@RequestParam String returnMapCode) {
        DataResult<List<ReturnMapDTO>> result = returnMapService.getAllReturnMapList(returnMapCode);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


   @Override
    public DataResult<List<ReturnMapDTO>> getAllReturnMapList(String returnMapCode) {
        List<ReturnMap> returnMapList = returnMapRepository.findByReturnMapCode(returnMapCode);

        return (DataResult<List<ReturnMapDTO>>) returnMapMapper.toReturnMapDTOList(returnMapList);
    }


@Mapper(componentModel = "spring")
public interface ReturnMapMapper {

	ReturnMapDTO toReturnMapDTO(ReturnMap entity);
	
	ReturnMap toReturnMap(ReturnMapDTO dto);
	
	List<ReturnMapDTO> toReturnMapDTOList(List<ReturnMap> entityList);
}
