
Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2026-01-22 20:39:26,097 ERROR [main][SpringApplication] Application run failed
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Class org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider does not implement the requested interface jakarta.persistence.spi.PersistenceProvider
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1818) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:607) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:371) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:207) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:970) ~[spring-context-6.2.6.jar:6.2.6]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627) ~[spring-context-6.2.6.jar:6.2.6]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.4.5.jar:3.4.5]
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:753) [spring-boot-3.4.5.jar:3.4.5]
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) [spring-boot-3.4.5.jar:3.4.5]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) [spring-boot-3.4.5.jar:3.4.5]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1362) [spring-boot-3.4.5.jar:3.4.5]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1351) [spring-boot-3.4.5.jar:3.4.5]
	at com.ykb.payments.bill.BillTransactionApplication.main(BillTransactionApplication.java:22) [classes/:?]
Caused by: java.lang.IncompatibleClassChangeError: Class org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider does not implement the requested interface jakarta.persistence.spi.PersistenceProvider
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.createNativeEntityManagerFactory(LocalContainerEntityManagerFactoryBean.java:390) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:419) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.afterPropertiesSet(AbstractEntityManagerFactoryBean.java:400) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.afterPropertiesSet(LocalContainerEntityManagerFactoryBean.java:366) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1865) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1814) ~[spring-beans-6.2.6.jar:6.2.6]
