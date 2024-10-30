
        JAXBElement<BigDecimal> tcKimlikNoElement =
                toJAXBElement("TCKimlikNo", BigDecimal.class, new BigDecimal(remoteRequest.getIdentityNo()));
        wsRequest.setKimlikNo(tcKimlikNoElement);

        Holder<ArrayOfBorcBilgisi> kentliBorcBilgileriniGetirResult = new Holder<>();

        Holder<Sonuc> sonuc = new Holder<>();

        getSeferihisarService().kentliBorcBilgileriniGetir(null, tcKimlikNoElement.getValue(), null, null, kentliBorcBilgileriniGetirResult, sonuc);
