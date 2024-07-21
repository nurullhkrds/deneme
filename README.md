@Tag(name = "Management ReturnMap BFF Controller")
@RequestMapping(RETURN_MAP_PATH)
@RestController
public class ReturnMapController {

    private final AdapterReturnMapClient adapterReturnMapClient;


    @Autowired
    public ReturnMapController(AdapterReturnMapClient adapterReturnMapClient) {
        this.adapterReturnMapClient = adapterReturnMapClient;
    }


    @Schema(description = "Get ReturnMaps By ReturnMapCode")
    @GetMapping("/getAllByReturnMapCode")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> getAllByReturnMapCode(@RequestParam String returnMapCode) {
        DataResult<List<ReturnMapDTO>> result = adapterReturnMapClient.getAllByReturnMapCode(returnMapCode);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @GetMapping("/getAll")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> getAll() {
        DataResult<List<ReturnMapDTO>> result = adapterReturnMapClient.getAll();
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @PostMapping("/createReturnMap")
    public ResponseEntity<DataResult<ReturnMapDTO>> createReturnMap(
            @RequestBody CreateReturnMapRequest request)
            throws MicroException {

        DataResult<ReturnMapDTO> result = adapterReturnMapClient.createReturnMap(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @PutMapping("/updateReturnMap")
    public ResponseEntity<DataResult<ReturnMapDTO>> updateReturnMap(
            @RequestBody UpdateReturnMapRequest request)
            throws MicroException {

        DataResult<ReturnMapDTO> result = adapterReturnMapClient.updateReturnMap(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }




    @GetMapping("/search")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> searchReturnMaps(
            @RequestParam(required = false) String returnMapCode,
            @RequestParam(required = false) String bankReturnCode,
            @RequestParam(required = false) String institutionErrorCode) {
        DataResult<List<ReturnMapDTO>> searhResult = adapterReturnMapClient.searchReturnMaps(returnMapCode, bankReturnCode, institutionErrorCode);
        return ResponseEntity.status(searhResult.getStatusCode()).body(searhResult);
    }

    @PostMapping("/copy")
    public ResponseEntity<Result> copyReturnMaps(@RequestBody CopyForIdsAndDataRequest request) {
        Result result = adapterReturnMapClient.copyReturnMaps(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);


    }

} ve @FeignClient(name = "PAYMENTS.BILL.bill-bff", path = RETURN_MAP_PATH, configuration = {MicroErrorDecoder.class})
public interface AdapterReturnMapClient {


    @GetMapping("/getAllByReturnMapCode")
    DataResult<List<ReturnMapDTO>> getAllByReturnMapCode(String returnMapCode);
    @GetMapping("/getAll")
    DataResult<List<ReturnMapDTO>> getAll();
    @PostMapping("/createReturnMap")
    DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest request);

    @PutMapping("/updateReturnMap")
    DataResult<ReturnMapDTO> updateReturnMap(UpdateReturnMapRequest request);

    @PostMapping("/copy")
    Result copyReturnMaps(CopyForIdsAndDataRequest request);
    @GetMapping("/search")
    DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionErrorCode);
}



rror starting ApplicationContext. To display the conditions report re-run your application with 'debug' enabled.
2024-07-21 20:34:25.283 ERROR 13440 --- [           main] o.s.boot.SpringApplication               : Application run failed

org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'returnMapController' defined in file [C:\Applications\micro_project\workspace\bill-bff\target\classes\com\ykb\payments\bill\adapter\web\ReturnMapController.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'com.ykb.payments.bill.transaction.client.AdapterReturnMapClient': Unexpected exception during bean creation; nested exception is java.lang.IllegalStateException: Method has too many Body parameters: public abstract com.ykb.payments.bill.common.utilities.DataResult com.ykb.payments.bill.transaction.client.AdapterReturnMapClient.searchReturnMaps(java.lang.String,java.lang.String,java.lang.String)
Warnings:
- 
	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:800) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.ConstructorResolver.autowireConstructor(ConstructorResolver.java:229) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.autowireConstructor(AbstractAutowireCapableBeanFactory.java:1372) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1222) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:582) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:542) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:335) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:234) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:333) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:208) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:955) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:920) ~[spring-context-5.3.27.jar:5.3.27]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:583) ~[spring-context-5.3.27.jar:5.3.27]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:147) ~[spring-boot-2.7.11.jar:2.7.11]
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:731) ~[spring-boot-2.7.11.jar:2.7.11]
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:408) ~[spring-boot-2.7.11.jar:2.7.11]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:307) ~[spring-boot-2.7.11.jar:2.7.11]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1303) ~[spring-boot-2.7.11.jar:2.7.11]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1292) ~[spring-boot-2.7.11.jar:2.7.11]
	at com.ykb.payments.bill.BillBFFApplication.main(BillBFFApplication.java:14) ~[classes/:na]
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'com.ykb.payments.bill.transaction.client.AdapterReturnMapClient': Unexpected exception during bean creation; nested exception is java.lang.IllegalStateException: Method has too many Body parameters: public abstract com.ykb.payments.bill.common.utilities.DataResult com.ykb.payments.bill.transaction.client.AdapterReturnMapClient.searchReturnMaps(java.lang.String,java.lang.String,java.lang.String)
Warnings:
- 
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:555) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:335) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:234) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:333) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:208) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.config.DependencyDescriptor.resolveCandidate(DependencyDescriptor.java:276) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.doResolveDependency(DefaultListableBeanFactory.java:1391) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.resolveDependency(DefaultListableBeanFactory.java:1311) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.ConstructorResolver.resolveAutowiredArgument(ConstructorResolver.java:887) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.ConstructorResolver.createArgumentArray(ConstructorResolver.java:791) ~[spring-beans-5.3.27.jar:5.3.27]
	... 19 common frames omitted
Caused by: java.lang.IllegalStateException: Method has too many Body parameters: public abstract com.ykb.payments.bill.common.utilities.DataResult com.ykb.payments.bill.transaction.client.AdapterReturnMapClient.searchReturnMaps(java.lang.String,java.lang.String,java.lang.String)
Warnings:
- 
	at feign.Util.checkState(Util.java:136) ~[feign-core-11.10.jar:na]
	at feign.Contract$BaseContract.parseAndValidateMetadata(Contract.java:143) ~[feign-core-11.10.jar:na]
	at org.springframework.cloud.openfeign.support.SpringMvcContract.parseAndValidateMetadata(SpringMvcContract.java:194) ~[spring-cloud-openfeign-core-3.1.6.jar:3.1.6]
	at feign.Contract$BaseContract.parseAndValidateMetadata(Contract.java:65) ~[feign-core-11.10.jar:na]
	at feign.ReflectiveFeign$ParseHandlersByName.apply(ReflectiveFeign.java:151) ~[feign-core-11.10.jar:na]
	at feign.ReflectiveFeign.newInstance(ReflectiveFeign.java:49) ~[feign-core-11.10.jar:na]
	at feign.Feign$Builder.target(Feign.java:205) ~[feign-core-11.10.jar:na]
	at org.springframework.cloud.openfeign.DefaultTargeter.target(DefaultTargeter.java:30) ~[spring-cloud-openfeign-core-3.1.6.jar:3.1.6]
	at org.springframework.cloud.openfeign.FeignClientFactoryBean.loadBalance(FeignClientFactoryBean.java:379) ~[spring-cloud-openfeign-core-3.1.6.jar:3.1.6]
	at org.springframework.cloud.openfeign.FeignClientFactoryBean.getTarget(FeignClientFactoryBean.java:427) ~[spring-cloud-openfeign-core-3.1.6.jar:3.1.6]
	at org.springframework.cloud.openfeign.FeignClientFactoryBean.getObject(FeignClientFactoryBean.java:402) ~[spring-cloud-openfeign-core-3.1.6.jar:3.1.6]
	at org.springframework.cloud.openfeign.FeignClientsRegistrar.lambda$registerFeignClient$0(FeignClientsRegistrar.java:235) ~[spring-cloud-openfeign-core-3.1.6.jar:3.1.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.obtainFromSupplier(AbstractAutowireCapableBeanFactory.java:1249) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1191) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:582) ~[spring-beans-5.3.27.jar:5.3.27]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:542) ~[spring-beans-5.3.27.jar:5.3.27]
	... 28 common frames omitted
