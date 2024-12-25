	@Bean
	@Primary
	@ConditionalOnProperty(value = "runtime.platform", havingValue = "pcf")
	public ConnectionFactory billRabbitPcfConnectionFactory() {
		//rabbitMQProperties.getServiceByKey(SERVICE_KEY).getName()
		
		final CfService rabbitService = new CfEnv()
				.findServiceByName(rabbitMQProperties.getServiceByKey(SERVICE_KEY).getName());
		final CfCredentials rabbitCredentials = rabbitService.getCredentials();

		final String plan = rabbitService.getPlan();

		final String name = rabbitCredentials.getName();
		final String hostname = rabbitCredentials.getHost();
		final String username = rabbitCredentials.getUsername();
		final String password = rabbitCredentials.getPassword();
		final String virtualHost = rabbitCredentials.getString("vhost");

		final CachingConnectionFactory connectionFactory = new CachingConnectionFactory();
		connectionFactory.setAddresses(hostname);
		connectionFactory.setUsername(username);
		connectionFactory.setPassword(password);
		connectionFactory.setVirtualHost(virtualHost);
		logger.info(
				"RabbitConnectionFactory [microPerfLogConnectionFactory] is initialized with Pcf configuration ServiceName: [{}] Name: [{}] Hostname: [{}] VirtualHost: [{}] Username: [{}] Plan: [{}]",
				rabbitMQProperties.getServiceByKey(SERVICE_KEY).getName(), name, hostname, virtualHost, username, plan);
		return connectionFactory;
	}
