 public NotifyBillPaymentResponse notifyBillPayment(NotifyBillPaymentRequest remoteRequest) {

        NotifyBillPaymentResponse response = new NotifyBillPaymentResponse();
        setBaseFields(remoteRequest, response);

        PaidBillDTO billDTO = remoteRequest.getBillDTO();

        LocalDate localDate = billDTO.getPaymentInformation().getPaymentDate();

        XMLGregorianCalendar xmlGregorianCalendar = getXmlGregorianCalendar(localDate);

        String nameSurname = billDTO.getSubscriberName();

        JAXBElement<String> adSoyad = toJAXBElement("AdSoyad", String.class, nameSurname);

        JAXBElement<BigDecimal> toplamTutarElement = toJAXBElement("ToplamTutar", BigDecimal.class, billDTO.getBillAmount());

        JAXBElement<XMLGregorianCalendar> tahsilatTarihiElement = toJAXBElement("TahsilatTarihi", XMLGregorianCalendar.class, xmlGregorianCalendar);

        JAXBElement<String> referansNoElement = toJAXBElement("ReferansNo", String.class, billDTO.getBankReferenceNo());

        JAXBElement<BigDecimal> identityNoElement = toJAXBElement("KimlikNo", BigDecimal.class, new BigDecimal(billDTO.getInfo8()));

        BorcTahsilati requestTahsilat = new BorcTahsilati();
        ArrayOfBorcDetayi arrayOfBorcDetayi = new ArrayOfBorcDetayi();
        BorcDetayi borcDetayi = new BorcDetayi();

        borcDetayi.setBeyanAnaId(new BigDecimal(billDTO.getInfo3()));
        borcDetayi.setBeyanSiraNo(new BigDecimal(billDTO.getInfo4()));
        borcDetayi.setBorcTutari(billDTO.getBillRecalculatedAmount());
        borcDetayi.setGecikmeTutari(billDTO.getCommissionAmount());
        borcDetayi.setHesapId(new BigDecimal(billDTO.getInfo2()));
        borcDetayi.setSistemId(new BigDecimal(billDTO.getInfo1()));
        borcDetayi.setTaksit(Integer.valueOf(billDTO.getInfo6()));
        borcDetayi.setToplamTutar(toplamTutarElement);
        borcDetayi.setVadeTarihi(getXmlGregorianCalendar(billDTO.getBillIssueDate()));
        borcDetayi.setYil(Integer.valueOf(billDTO.getInfo5()));

        arrayOfBorcDetayi.getBorcDetayi().add(borcDetayi);

        JAXBElement<ArrayOfBorcDetayi> borcDetayiJAXBElement = new JAXBElement<>(new QName("BorcDetaylari"), ArrayOfBorcDetayi.class, arrayOfBorcDetayi);

        requestTahsilat.setAdSoyad(adSoyad);
        requestTahsilat.setBorcDetaylari(borcDetayiJAXBElement);
        requestTahsilat.setKentliId(new BigDecimal(billDTO.getInfo7()));
        requestTahsilat.setReferansNo(referansNoElement);
        requestTahsilat.setKimlikNo(identityNoElement);
        requestTahsilat.setTahsilatTarihi(tahsilatTarihiElement);
        requestTahsilat.setTarih(xmlGregorianCalendar);
        requestTahsilat.setToplamTutar(billDTO.getBillAmount());

        Holder<BigDecimal> kentliBorclariniOdeResult = new Holder<>();
        Holder<Sonuc> sonuc = new Holder<>();

        getSeferihisarService().kentliBorclariniOde(requestTahsilat, kentliBorclariniOdeResult, sonuc);
        String institutionCodeControl=sonuc.value.getKod().value();
         String responseInternalResultCode = String.valueOf((sonuc.value).getHataKodu());
        resolveResponseMessage(responseInternalResultCode, response);

        if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus()) && institutionCodeControl.equals("Basarili")) {
            billDTO.setInfo9((kentliBorclariniOdeResult.value).toString());
            response.setBillDTO(billDTO);
        }
        response.setInstitutionResultDetail(sonuc.value.getMesaj().getValue());
        setRemoteResponseData(response, remoteRequest, PYMLogUtil.convertObjectToJsonString(requestTahsilat), PYMLogUtil.convertObjectToJsonString(kentliBorclariniOdeResult));
        return response;
    }
