BigDecimal billNo = new BigDecimal(remoteRequest.getBillNo());
JAXBElement<BigDecimal> kentliIdElement = new JAXBElement<>(
    new QName("kentliId"),
    BigDecimal.class,
    billNo
);

wsRequest.setKentliId(kentliIdElement);
