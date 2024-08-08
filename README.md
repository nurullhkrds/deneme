    @Bean
    @Primary
    public RabbitTemplate billTransactionRabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate rabbitTemplate = new RabbitTemplate();
        rabbitTemplate.setConnectionFactory(connectionFactory);
        rabbitTemplate.setMessageConverter(jsonMessageConverter());
        rabbitTemplate.setChannelTransacted(false);
        declareQueues(connectionFactory);
        return rabbitTemplate;
    }
