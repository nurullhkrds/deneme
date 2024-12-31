rabbitmq:
  enabled: true
  services:
    bill-rabbitmq:
      name: PAYMENTS.BILL-TST.RabbitMQ
      enabled: false
      consumers:
        remoteLogEvent:
          minConcurrentConsumers: 4
          maxConcurrentConsumers: 8
          prefetchCount: 20
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
