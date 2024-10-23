  JAXBElement<BigDecimal> toplamTutarElement = new JAXBElement<>(
                new QName("ToplamTutar"),
                BigDecimal.class,
                billDTO.getBillAmount()
        );
