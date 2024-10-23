arrayOfBorcDetayi.getBorcDetayi().stream().map(borc -> {
    borc.setHesapId(new BigDecimal(billDTO.getInfo1()));
    borc.setSistemId(new BigDecimal(billDTO.getInfo2()));
    borc.setBeyanAnaId(new BigDecimal(billDTO.getInfo3()));
    borc.setBeyanSiraNo(new BigDecimal(billDTO.getInfo4()));
    borc.setTaksit(Integer.valueOf(billDTO.getInfo5()));
    borc.setYil(Integer.valueOf(billDTO.getInfo7()));
    borc.setVadeTarihi(getXmlGregorianCalendar(billDTO.getBillIssueDate()));
    borc.setBorcTutari(billDTO.getBillRecalculatedAmount());
    borc.setGecikmeTutari(billDTO.getCommissionAmount());
    borc.setToplamTutar(toplamTutarElement.getValue());
    return borc;
}).forEach(arrayOfBorcDetayi.getBorcDetayi()::add);

// Create JAXBElement<ArrayOfBorcDetayi> for BorcDetayi
JAXBElement<ArrayOfBorcDetayi> borcDetayiJAXBElement = new JAXBElement<>(
    new QName("BorcDetayi"),
    ArrayOfBorcDetayi.class,
    arrayOfBorcDetayi
);
