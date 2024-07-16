spring:
  application:
    name: PAYMENTS.BILL.bill-transaction
  cloud:
    services:
      registrationMethod: direct
    discovery:
      client:
        simple:
          instances:
            -COREBANKING.COMMISSION.calculation[0].uri: url
            -COREBANKING.ACCOUNT.account[0].uri:url
            -PAYMENTS.BILL.bill-adapter[0].uri: url
            -COREBANKING.ACCOUNT.provision-main[0].uri: url
            -CARDPAYMENTS.SWT.swt-switch-integration[0].uri: url
            -PAYMENTS.COMMON.limitation-service[0].uri: url
  datasource:
    username: BILL
    password: sssss
    url: jdbc:oracle:thin:@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=oprkbarcdbt.sys.yapikredi.com.tr)(PORT=1818))(CONNECT_DATA=(SERVER=dedicated)(SERVICE_NAME=SRVTEST_NYSU)))
    driverClassName: oracle.jdbc.OracleDriver
    hikari:
      maximum-pool-size: 10
      minimum-idle: 1
      data-source-properties:
        oracle.jdbc.ReadTimeout: 30000
        oracle.net.READ_TIMEOUT: 30000
        oracle.net.CONNECT_TIMEOUT: 30000
        "[v$session.program]": ${spring.application.name}
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: none #none || update || create
feign:
  client:
    config:
      default:
        connectTimeout: 5000
        readTimeout: 30000
        loggerLevel: basic
  hystrix:
    enabled: false
hystrix:
  command:
    default:
      execution:
        timeout:
          enabled: false
logging:
  level:
    com:
      netflix:
        discovery: OFF
    org:
      springframework:
        security: ERROR
        web: ERROR
springdoc:
  api-docs:
    path: /actuator/api-docs
management:
  endpoints:
    web:
      exposure:
        include: '*'
cache:
  redis:
    serviceName: PAYMENTS.BILL-TST.Redis
    institutionFeatureValue:
      ttl: 12
    institutionFeatureList:
      ttl: 12
    getProcessChannelForProcess:
      ttl: 12
    getInstitutionForProcess:
      ttl: 12
    getInstitutionChannelForProcess:
      ttl: 12
    getInstitutionProcess:
      ttl: 12
    getInstitutionChannelProcess:
      ttl: 12
    getInstitutionDebtTypeForProcess:
      ttl: 12
    getInstitutionById:
      ttl: 12
    institutionUserInterfaceList:
      ttl: 12
    findChannelByChannelCode:
      ttl: 12
## pcf env redis configuration
runtime:
  platform: local
## RabbitMQ Configuration
rabbitmq:
  enabled: true
  services:
    billtransaction-rabbitmq:
      name: PAYMENTS.BILL-TST.RabbitMQ
      enabled: false
      consumers:
        paymentNotificationEvent:
          minConcurrentConsumers: 2
          maxConcurrentConsumers: 4
          prefetchCount: 10
      producers:
        paymentNotificationEvent:
          exchangeName: billtransaction-direct-exchange
          routingKey: payment-notification-event
        creditCardProvisionACKEvent: # Eksik yapılandırma değeri eklendi
          exchangeName: creditCardProvisionACK-exchange
          routingKey: creditCardProvisionACK-event
      queues:
        paymentNotificationEvent:
          declare: true
          name: payment-notification-queue
          durable: true
          routingKey: payment-notification-event
          arguments:
            x-message-ttl: 300000
          exchange:
            name: billtransaction-direct-exchange
            durable: true
            type: direct
external:
  billPaymentRestFacade:
    address: url
    service:
      readTimeout: 30000
      connectTimeout: 30000
