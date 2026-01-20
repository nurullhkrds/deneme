<project xmlns="http://maven.apache.org/POM/4.0.0"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ykb.payments.bill</groupId>
	<artifactId>bill-adapter</artifactId>
	<version>${pkg.version}</version>
	<name>${pkg.title}</name>
	<description>${pkg.description}</description>

	<parent>
		<groupId>com.ykb.sdlc.pom</groupId>
		<artifactId>root-pom-spring-boot</artifactId>
		<version>3.4.5-SNAPSHOT</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<maven.test.skip>false</maven.test.skip>
		<!-- Package information -->
		<java.version>17</java.version>
		<pkg.title>Bill Adapter Micro Service</pkg.title>
		<pkg.description>Bill Adapter Micro Service</pkg.description>
		<pkg.version>0.0.1</pkg.version>
		<pkg.build.javaVersion>17</pkg.build.javaVersion>

		<!-- Dependency information -->
		<dep.spring-cloud.version>2024.0.0</dep.spring-cloud.version>
        <dep.spring-cloud-services>4.2.0</dep.spring-cloud-services>
		<ykb-micro-tools.version>3.4.5-LIV-125</ykb-micro-tools.version>

		<dep.swagger.version>2.6.1</dep.swagger.version>
		<dep.springfox-swagger-ui.version>3.0.0</dep.springfox-swagger-ui.version>

		<dep.cxf.version>4.0.3</dep.cxf.version>

		<dep.org.mapstruct.version>1.4.2.Final</dep.org.mapstruct.version>

		<dep.gson-java8-datatype.version>1.0.3</dep.gson-java8-datatype.version>
		<dep.gson-java8-datatype.version>1.0.3</dep.gson-java8-datatype.version>

		<dep.feign-httpclient.version>9.4.0</dep.feign-httpclient.version>

		<dep.lombok.version>1.18.26</dep.lombok.version>

		<dep.junit.version>5.8.2</dep.junit.version>

		<dep.jacoco-maven-plugin.version>0.8.8</dep.jacoco-maven-plugin.version>
		<dep.cb.version>1.7.0</dep.cb.version>

		<swagger-ui.version>2.7.0</swagger-ui.version>

		<!-- Maven Plugin Dependency information -->
		<dep.maven.checkstyle.plugin.version>3.3.1</dep.maven.checkstyle.plugin.version>
		<dep.maven.pmd.plugin.version>3.20.0</dep.maven.pmd.plugin.version>
		<dep.puppycrawl.checkstyle.version>10.12.3</dep.puppycrawl.checkstyle.version>

		<!-- Sonar configs -->
		<sonar.dynamicAnalysis>reuseReports</sonar.dynamicAnalysis>
		<sonar.java.coveragePlugin>jacoco</sonar.java.coveragePlugin>
		<sonar.jacoco.reportPath>${project.basedir}/target/jacoco.exec</sonar.jacoco.reportPath>
		<sonar.language>java</sonar.language>

		<!-- Sonar Duplication Exclusions -->
		<sonar.cpd.exclusions>
			**/dto/**/*,
			**/domain/**/*,
			**/entity/**/*,
			**/model/**/*,
			**/request/**/*,
			**/response/**/*
		</sonar.cpd.exclusions>

		<dep.snapshot.version>TEST-SNAPSHOT</dep.snapshot.version>
	</properties>

	<dependencies>

		<dependency>
			<groupId>com.ykb.payments.bill</groupId>
			<artifactId>bill-remote</artifactId>
			<!--<version>1.0.0-TST-247</version>-->
			<version>1.0.0</version>
		</dependency>

            <!-- Locali görür -->


<!--                         <dependency>-->
<!--                             <groupId>com.ykb.payments.bill</groupId>-->
<!--                             <artifactId>bill-remote</artifactId>-->
<!--                             <version>1.0.0</version>-->
<!--                         </dependency>-->



                       <dependency>
                           <groupId>com.ykb.payments.bill</groupId>
                           <artifactId>bill-common</artifactId>
                           <!-- <version>1.0.0-TST-104</version> -->
                           <version>1.0.0</version>
                       </dependency>

                        <!-- spring boot dependencies -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<dependency>
			<groupId>jakarta.persistence</groupId>
			<artifactId>jakarta.persistence-api</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web-services</artifactId>
		</dependency>




		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-aop</artifactId>
		</dependency>

		<!-- spring boot dependencies -->

		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-tools-common</artifactId>
			<version>${dep.cxf.version}</version>
			<scope>provided</scope>
		</dependency>

		<dependency>
			<groupId>org.objenesis</groupId>
			<artifactId>objenesis</artifactId>
			<version>2.1</version>
		</dependency>

		<dependency>
			<groupId>com.esotericsoftware.minlog</groupId>
			<artifactId>minlog</artifactId>
			<version>1.2</version>
		</dependency>

		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-spring-boot-starter-jaxws</artifactId>
			<version>${dep.cxf.version}</version>
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
		</dependency>

		<!-- 		<dependency> -->
		<!-- 			<groupId>com.fasterxml.jackson.datatype</groupId> -->
		<!-- 			<artifactId>jackson-datatype-jsr310</artifactId> -->
		<!-- 		</dependency> -->

		<dependency>
			<groupId>org.springdoc</groupId>
			<artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
			<version>${swagger-ui.version}</version>
		</dependency>

		<!-- Service Catalog Starter (service-info.yml dosyasının okunması için
			kullanılır.) -->
		<dependency>
			<groupId>com.ykb.architecture.services</groupId>
			<artifactId>service-catalog-starter-2-release</artifactId>
		</dependency>

		<dependency>
			<groupId>net.dongliu</groupId>
			<artifactId>gson-java8-datatype</artifactId>
			<version>${dep.gson-java8-datatype.version}</version>
		</dependency>

		<dependency>
			<groupId>org.hibernate.orm</groupId>
			<artifactId>hibernate-envers</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-openfeign</artifactId>
		</dependency>


		<!-- 		<dependency> -->
		<!-- 			<groupId>io.springfox</groupId> -->
		<!-- 			<artifactId>springfox-swagger2</artifactId> -->
		<!-- 			<version>${dep.springfox-swagger-ui.version}</version> -->
		<!-- 		</dependency> -->

		<dependency>
			<groupId>org.mapstruct</groupId>
			<artifactId>mapstruct</artifactId>
			<version>${dep.org.mapstruct.version}</version>
		</dependency>

		<!-- 		<dependency> -->
		<!-- 			<groupId>io.springfox</groupId> -->
		<!-- 			<artifactId>springfox-swagger-ui</artifactId> -->
		<!-- 			<version>${dep.springfox-swagger-ui.version}</version> -->
		<!-- 		</dependency> -->

		<!--       <dependency> -->
		<!--             <groupId>io.springfox</groupId> -->
		<!--             <artifactId>springfox-boot-starter</artifactId> -->
		<!--             <version>3.0.0</version> -->
		<!--         </dependency> -->

		<dependency>
			<groupId>com.h2database</groupId>
			<artifactId>h2</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>com.oracle.database.jdbc</groupId>
			<artifactId>ojdbc8</artifactId>
            <version>19.3.0.0</version>

        </dependency>



		<dependency>
			<groupId>com.ykb.architecture.services</groupId>
			<artifactId>micro-error</artifactId>
		</dependency>



		<dependency>
			<groupId>io.micrometer</groupId>
			<artifactId>micrometer-core</artifactId>
		</dependency>

		<dependency>
			<groupId>io.micrometer</groupId>
			<artifactId>micrometer-registry-prometheus</artifactId>
		</dependency>

		<dependency>
			<groupId>io.prometheus</groupId>
			<artifactId>simpleclient</artifactId>
		</dependency>

		<dependency>
			<groupId>io.pivotal.spring.cloud</groupId>
			<artifactId>spring-cloud-services-starter-config-client</artifactId>
		</dependency>

		<dependency>
			<groupId>io.pivotal.spring.cloud</groupId>
			<artifactId>spring-cloud-services-starter-service-registry</artifactId>
		</dependency>

		<!-- Redis Dependencies -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-redis</artifactId>
		</dependency>

		<dependency>
			<groupId>redis.clients</groupId>
			<artifactId>jedis</artifactId>
		</dependency>

		<!-- Local Embedded Redis Dependecy -->
		<dependency>
			<groupId>it.ozimov</groupId>
			<artifactId>embedded-redis</artifactId>
			<version>0.7.2</version>
		</dependency>

		<!-- Redis Dependencies -->

		<!-- RabbitMQ Dependencies -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-amqp</artifactId>
		</dependency>
		<!-- RabbitMQ Dependencies -->

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-configuration-processor</artifactId>
			<optional>true</optional>
		</dependency>

		<dependency>
			<groupId>org.apache.cxf</groupId>
			<artifactId>cxf-spring-boot-autoconfigure</artifactId>
			<version>${dep.cxf.version}</version>
		</dependency>

		<!-- Testing Dependencies -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<!-- Sherlog Dependencies -->
<!--		<dependency>-->
<!--			<groupId>com.ykb.architecture.services</groupId>-->
<!--			<artifactId>sherlog-spring-boot-starter</artifactId>-->
<!--		</dependency>-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-log4j2</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.springframework.boot</groupId>
					<artifactId>spring-boot-starter-logging</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<!-- Sherlog Dependencies -->

		<!--Circuit Breaker Dependencies -->

		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-core</artifactId>
			<version>${dep.cb.version}</version>
		</dependency>

		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-spring-boot3</artifactId>
		</dependency>

		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-circuitbreaker</artifactId>
			<version>${dep.cb.version}</version>
		</dependency>

		<dependency>
			<groupId>io.github.resilience4j</groupId>
			<artifactId>resilience4j-timelimiter</artifactId>
			<version>${dep.cb.version}</version>
		</dependency>

		<!--Circuit Breaker Dependencies -->

	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${dep.spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
			<dependency>
				<groupId>io.pivotal.spring.cloud</groupId>
				<artifactId>spring-cloud-services-dependencies</artifactId>
				<version>${dep.spring-cloud-services}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
			<dependency>
				<groupId>com.ykb.architecture.services</groupId>
				<artifactId>ykb-micro-tools</artifactId>
				<scope>import</scope>
				<type>pom</type>
				<version>${ykb-micro-tools.version}</version>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<annotationProcessorPaths>
						<path>
							<groupId>org.projectlombok</groupId>
							<artifactId>lombok</artifactId>
							<version>${dep.lombok.version}</version>
						</path>
						<path>
							<groupId>org.mapstruct</groupId>
							<artifactId>mapstruct-processor</artifactId>
							<version>${dep.org.mapstruct.version}</version>
						</path>
					</annotationProcessorPaths>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-failsafe-plugin</artifactId>
				<executions>
					<execution>
						<goals>
							<goal>integration-test</goal>
							<goal>verify</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<version>3.2.2</version><!--$NO-MVN-MAN-VER$ -->
				<configuration>
					<properties>
						<configurationParameters>
							junit.jupiter.execution.parallel.enabled = true
							junit.jupiter.execution.parallel.mode.default = same_thread
							junit.jupiter.execution.parallel.mode.classes.default = concurrent
							junit.jupiter.execution.parallel.config.strategy = fixed
							junit.jupiter.execution.parallel.config.fixed.parallelism = 4
						</configurationParameters>
					</properties>
				</configuration>
				<dependencies>
					<dependency>
						<groupId>org.junit.jupiter</groupId>
						<artifactId>junit-jupiter-engine</artifactId>
						<version>${dep.junit.version}</version>
					</dependency>
					<dependency>
						<groupId>org.junit.platform</groupId>
						<artifactId>junit-platform-surefire-provider</artifactId>
						<version>1.3.1</version>
					</dependency>
				</dependencies>
			</plugin>

			<plugin>
				<groupId>org.jacoco</groupId>
				<artifactId>jacoco-maven-plugin</artifactId>
				<version>${dep.jacoco-maven-plugin.version}</version><!--$NO-MVN-MAN-VER$ -->
				<configuration>
					<destFile>${sonar.jacoco.reportPath}</destFile>
					<append>true</append>
				</configuration>
				<executions>
					<execution>
						<goals>
							<goal>prepare-agent</goal>
						</goals>
					</execution>
					<execution>
						<id>report</id>
						<phase>test</phase>
						<goals>
							<goal>report</goal>
						</goals>
					</execution>
				</executions>
			</plugin>

		</plugins>




Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled.
2026-01-20 17:13:27,261 ERROR [main][SpringApplication] Application run failed
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: [PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.MappingException: Could not instantiate id generator [entity-name=org.hibernate.envers.DefaultRevisionEntity]
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
	at com.ykb.payments.bill.adapter.BillAdapterApplication.main(BillAdapterApplication.java:19) [classes/:?]
Caused by: jakarta.persistence.PersistenceException: [PersistenceUnit: default] Unable to build Hibernate SessionFactory; nested exception is org.hibernate.MappingException: Could not instantiate id generator [entity-name=org.hibernate.envers.DefaultRevisionEntity]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:431) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.afterPropertiesSet(AbstractEntityManagerFactoryBean.java:400) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.afterPropertiesSet(LocalContainerEntityManagerFactoryBean.java:366) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1865) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1814) ~[spring-beans-6.2.6.jar:6.2.6]
	... 15 more
Caused by: org.hibernate.MappingException: Could not instantiate id generator [entity-name=org.hibernate.envers.DefaultRevisionEntity]
	at org.hibernate.id.factory.internal.StandardIdentifierGeneratorFactory.createIdentifierGenerator(StandardIdentifierGeneratorFactory.java:237) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.id.factory.internal.IdentifierGeneratorUtil.createLegacyIdentifierGenerator(IdentifierGeneratorUtil.java:42) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.mapping.SimpleValue.createGenerator(SimpleValue.java:402) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.internal.SessionFactoryImpl.lambda$createGenerators$1(SessionFactoryImpl.java:467) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.accept(ForEachOps.java:183) ~[?:?]
	at java.base/java.util.stream.ReferencePipeline$2$1.accept(ReferencePipeline.java:179) ~[?:?]
	at java.base/java.util.HashMap$ValueSpliterator.forEachRemaining(HashMap.java:1779) ~[?:?]
	at java.base/java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:509) ~[?:?]
	at java.base/java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:499) ~[?:?]
	at java.base/java.util.stream.ForEachOps$ForEachOp.evaluateSequential(ForEachOps.java:150) ~[?:?]
	at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.evaluateSequential(ForEachOps.java:173) ~[?:?]
	at java.base/java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234) ~[?:?]
	at java.base/java.util.stream.ReferencePipeline.forEach(ReferencePipeline.java:596) ~[?:?]
	at org.hibernate.internal.SessionFactoryImpl.createGenerators(SessionFactoryImpl.java:465) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.internal.SessionFactoryImpl.<init>(SessionFactoryImpl.java:280) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.boot.internal.SessionFactoryBuilderImpl.build(SessionFactoryBuilderImpl.java:463) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1517) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider.createContainerEntityManagerFactory(SpringHibernateJpaPersistenceProvider.java:66) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.createNativeEntityManagerFactory(LocalContainerEntityManagerFactoryBean.java:390) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:419) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.afterPropertiesSet(AbstractEntityManagerFactoryBean.java:400) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.afterPropertiesSet(LocalContainerEntityManagerFactoryBean.java:366) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1865) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1814) ~[spring-beans-6.2.6.jar:6.2.6]
	... 15 more
Caused by: org.hibernate.HibernateException: Could not fetch the SequenceInformation from the database
	at org.hibernate.engine.jdbc.env.internal.ExtractedDatabaseMetaDataImpl.sequenceInformationList(ExtractedDatabaseMetaDataImpl.java:307) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.engine.jdbc.env.internal.ExtractedDatabaseMetaDataImpl.getSequenceInformationList(ExtractedDatabaseMetaDataImpl.java:151) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.id.enhanced.SequenceStyleGenerator.getSequenceIncrementValue(SequenceStyleGenerator.java:611) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.id.enhanced.SequenceStyleGenerator.adjustIncrementSize(SequenceStyleGenerator.java:281) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.id.enhanced.SequenceStyleGenerator.configure(SequenceStyleGenerator.java:222) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.id.factory.internal.StandardIdentifierGeneratorFactory.createIdentifierGenerator(StandardIdentifierGeneratorFactory.java:231) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.id.factory.internal.IdentifierGeneratorUtil.createLegacyIdentifierGenerator(IdentifierGeneratorUtil.java:42) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.mapping.SimpleValue.createGenerator(SimpleValue.java:402) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.internal.SessionFactoryImpl.lambda$createGenerators$1(SessionFactoryImpl.java:467) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.accept(ForEachOps.java:183) ~[?:?]
	at java.base/java.util.stream.ReferencePipeline$2$1.accept(ReferencePipeline.java:179) ~[?:?]
	at java.base/java.util.HashMap$ValueSpliterator.forEachRemaining(HashMap.java:1779) ~[?:?]
	at java.base/java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:509) ~[?:?]
	at java.base/java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:499) ~[?:?]
	at java.base/java.util.stream.ForEachOps$ForEachOp.evaluateSequential(ForEachOps.java:150) ~[?:?]
	at java.base/java.util.stream.ForEachOps$ForEachOp$OfRef.evaluateSequential(ForEachOps.java:173) ~[?:?]
	at java.base/java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234) ~[?:?]
	at java.base/java.util.stream.ReferencePipeline.forEach(ReferencePipeline.java:596) ~[?:?]
	at org.hibernate.internal.SessionFactoryImpl.createGenerators(SessionFactoryImpl.java:465) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.internal.SessionFactoryImpl.<init>(SessionFactoryImpl.java:280) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.boot.internal.SessionFactoryBuilderImpl.build(SessionFactoryBuilderImpl.java:463) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1517) ~[hibernate-core-6.6.13.Final.jar:6.6.13.Final]
	at org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider.createContainerEntityManagerFactory(SpringHibernateJpaPersistenceProvider.java:66) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.createNativeEntityManagerFactory(LocalContainerEntityManagerFactoryBean.java:390) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:419) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.afterPropertiesSet(AbstractEntityManagerFactoryBean.java:400) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.afterPropertiesSet(LocalContainerEntityManagerFactoryBean.java:366) ~[spring-orm-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.invokeInitMethods(AbstractAutowireCapableBeanFactory.java:1865) ~[spring-beans-6.2.6.jar:6.2.6]
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.initializeBean(AbstractAutowireCapableBeanFactory.java:1814) ~[spring-beans-6.2.6.jar:6.2.6]
	... 15 more
Caused by: java.sql.SQLException: Non supported character set (add orai18n.jar in your classpath): WE8ISO8859P9
	</build>

</project>
