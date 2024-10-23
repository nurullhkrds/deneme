 PaidBillDTO billDTO = remoteRequest.getBillDTO();


        LocalDate localDate = billDTO.getPaymentInformation().getPaymentDate();
        GregorianCalendar gregorianCalendar = GregorianCalendar.from(localDate.atStartOfDay(ZoneId.systemDefault()));
        XMLGregorianCalendar xmlGregorianCalendar ;
        try {
            xmlGregorianCalendar = DatatypeFactory.newInstance().newXMLGregorianCalendar(gregorianCalendar);
        } catch (DatatypeConfigurationException e) {
            throw new RuntimeException(e);
        }

        JAXBElement<XMLGregorianCalendar> tahsilatTarihi = new JAXBElement<>(
                new QName("TahsilatTarihi"),
                XMLGregorianCalendar.class,
                xmlGregorianCalendar
        );

        String nameSurname = billDTO.getSubscriberName();

        JAXBElement<String> adSoyad = new JAXBElement<>(
                new QName("AdSoyad"),
                String.class,
                nameSurname
        );
        JAXBElement<BigDecimal> toplamTutarElement = new JAXBElement<>(
                new QName("ToplamTutar"),
                BigDecimal.class,
                billDTO.getBillAmount()
        );
        ArrayOfBorcDetayi arrayOfBorcDetayi= new ArrayOfBorcDetayi();
        
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
            borc.setToplamTutar(toplamTutarElement);
            
        })
        JAXBElement<ArrayOfBorcDetayi> borcDetayiJAXBElement=new JAXBElement(
        
        );
