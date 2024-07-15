spring:
  application:
    name: PAYMENTS.BILL.bill-adapter
  cloud:
    services:
      registrationMethod: direct
  datasource:
    username: BILL
    password: Bolefppe1203
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
    org:
      slf4j: WARN
      springframework:
        security: ERROR
        web: ERROR
springdoc:
  api-docs:
    path: /actuator/api-docs
cache:
  redis:
    serviceName: PAYMENTS.BILL-TST.Redis
    institution:
      ttl: 12
    institutionAdapter:
      ttl: 12
    adapterService:
      ttl: 12
    returnMap:
      ttl: 12
    tokenDefinition:
      ttl: 12
runtime:
  platform: pcf
rabbitmq:
  enabled: true
  services:
    bill-rabbitmq:
      name: PAYMENTS.BILL-TST.RabbitMQ
      enabled: true
      consumers:
        tokenInvalidEvent:
          minConcurrentConsumers: 2
          maxConcurrentConsumers: 4
          prefetchCount: 10
      producers:
        remoteLogEvent:
          exchangeName: bill-direct-exchange
          routingKey: remote-log-event
        tokenInvalidEvent:
          exchangeName: bill-direct-exchange
          routingKey: token-invalid-event
      queues:
        remoteLogEvent:
          declare: true
          name: remote-log-queue
          durable: true
          routingKey: remote-log-event
          arguments:
            x-message-ttl: 300000
          exchange:
            name: bill-direct-exchange
            durable: true
            type: direct
        tokenInvalidEvent:
          declare: true
          name: token-invalid-queue
          durable: true
          routingKey: token-invalid-event
          arguments:
            x-message-ttl: 300000
          exchange:
            name: bill-direct-exchange
            durable: true
            type: direct
management:
  health:
    circuitbreakers:
      enabled: true
    ratelimiters:
      enabled: true
  endpoints:
    web:
      exposure:
        include: '*'
  endpoint:
    health:
      show-details: always
resilience4j:
  enabled: true
  circuitbreaker:
    configs:
      default:
        registerHealthIndicator: true
smoke:
  tests:
    enabled: false
junit:
  institutionTest:
    enabled: false
metric:
  enabled: true
  environment: test
