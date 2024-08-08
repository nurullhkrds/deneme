    ExchangeSpec exchangeSpec=new ExchangeSpec();
        exchangeSpec.setName("testExchange");
        exchangeSpec.setType("direct");
        exchangeSpec.setDurable(true);
        queueSpec.setExchange(exchangeSpec);
