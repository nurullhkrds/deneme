  private void declareQueues(ConnectionFactory connectionFactory) {
        if (rabbitMQProperties.getServiceByKey(SERVICE_KEY).getQueues() == null) {
            return;
        }

        try (Channel channel = RabbitMQUtil.getChannel(connectionFactory)) {
            rabbitMQProperties.getServiceByKey(SERVICE_KEY).getQueues().entrySet().stream().filter(spec -> spec.getValue().isDeclare()).forEach(spec -> declareQueue(channel, spec.getValue()));
        } catch (IOException | TimeoutException e) {
            logger.error("An error occurred while declaring queues.", e);
        }
    }
