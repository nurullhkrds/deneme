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
        paymentCancelNotificationEvent:   # <--- Bu kısmı ekleyin
          exchangeName: billtransaction-direct-exchange-cancel  # Uygun bir exchangeName değeri belirleyin
          routingKey: payment-cancel-notification-event  # Uygun bir routingKey değeri belirleyin
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
