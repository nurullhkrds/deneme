org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'com.ykb.payments.bill.remote.institution.service.InstitutionRelationService' available

	at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:394)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:385)
	at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1290)
.bill.common.util.SpringUtil.getBean(SpringUtil.java:23)
.bill.remote.adapter.integration.CommonIntegrationTest.getInstitutionServiceDTO(CommonIntegrationTest.java:706)



    private InstitutionServiceDTO getInstitutionServiceDTO(String serviceType) {
        String url = virtualServiceUrl != null ? virtualServiceUrl : virtualServiceUrlList.get(serviceType);
        InstitutionServiceDTO t = new InstitutionServiceDTO();
        t.setUrl(url);
        t.setServiceType(serviceType);
        t.setServiceTimeoutDTO(new ServiceTimeoutDTO(connectionTimeout, readTimeout));
        t.setUserName(userName);
        t.setPword(password);
        t.setParamaters(paramsList);

        adapter.setInstitutionService(t);

        if (token != null) {
            TokenDefinitionDTO tokenDefinition = new TokenDefinitionDTO();
            tokenDefinition.setRunType(tokenRunType);
            tokenDefinition.setDefinitionKey("key");
            adapter.setTokenDefinition(tokenDefinition);
            try {
                TokenDTO tokenDTO = new TokenDTO();
                tokenDTO.setTokenDefinition(tokenDefinition);
                tokenDTO.setTokenData(token);
                tokenDTO.setId(1L);
                if (tokenRunType.equals(EnumTokenRunType.WITH_LOCK)) {
                    Mockito.when(tokenService.getTokenByDefinitionKey(ArgumentMatchers.anyString())).thenReturn(tokenDTO);
                } else {
                    Mockito.when(tokenService.getCurrentLatestActiveToken(ArgumentMatchers.anyString())).thenReturn(tokenDTO);
                    if (StringUtils.isEmpty(token)) {
                        Mockito.when(tokenService.saveToken(ArgumentMatchers.anyString(), ArgumentMatchers.anyString())).thenReturn(tokenDTO);
                    }
                }
            } catch (MicroException e) {
                e.printStackTrace();
            }
        }

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setParamaters(paramsList);

        Mockito.when(SpringUtil.getBean(InstitutionRelationService.class).getInstitution(ArgumentMatchers.anyString(), ArgumentMatchers.anyString())).thenReturn(institutionDTO);
        return t;
    }


@Component
public class SpringUtil implements ApplicationContextAware {

	private static ApplicationContext appContext;

	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		synchronized (this) {
			if (appContext == null) {
				appContext = applicationContext;
			}
		}
	}

	public static <T> T getBean(Class<T> clazz) {
		return appContext.getBean(clazz);
	}

	public static <T> T getBean(String qualifier, Class<T> clazz) {
		return appContext.getBean(qualifier, clazz);
	}
	
	public static ApplicationContext getApplicationContext() {
		return appContext;
	}
}
