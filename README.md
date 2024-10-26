import javax.xml.bind.JAXBContext;
import javax.xml.bind.JAXBElement;
import javax.xml.bind.Marshaller;
import javax.xml.namespace.QName;

public NotifyBillPaymentResponse notifyBillPayment(NotifyBillPaymentRequest remoteRequest) {

    NotifyBillPaymentResponse response = new NotifyBillPaymentResponse();
    setBaseFields(remoteRequest, response);

    PaidBillDTO billDTO = remoteRequest.getBillDTO();
    LocalDate localDate = billDTO.getPaymentInformation().getPaymentDate();
    XMLGregorianCalendar xmlGregorianCalendar = getXmlGregorianCalendar(localDate);

    // Prefix'li QName ile oluşturulan JAXBElement'ler
    JAXBElement<String> adSoyad = toJAXBElement(new QName("http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts", "AdSoyad", "ns2"), String.class, billDTO.getSubscriberName());
    JAXBElement<BigDecimal> toplamTutarElement = toJAXBElement(new QName("http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts", "ToplamTutar", "ns2"), BigDecimal.class, billDTO.getBillAmount());
    JAXBElement<XMLGregorianCalendar> tahsilatTarihiElement = toJAXBElement(new QName("http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts", "TahsilatTarihi", "ns2"), XMLGregorianCalendar.class, xmlGregorianCalendar);
    JAXBElement<String> referansNoElement = toJAXBElement(new QName("http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts", "ReferansNo", "ns2"), String.class, billDTO.getBankReferenceNo());
    JAXBElement<BigDecimal> identityNoElement = toJAXBElement(new QName("http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts", "KimlikNo", "ns2"), BigDecimal.class, new BigDecimal(billDTO.getInfo8()));

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

    // Prefix'li BorcDetaylari JAXBElement'i
    JAXBElement<ArrayOfBorcDetayi> borcDetayiJAXBElement = new JAXBElement<>(
        new QName("http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts", "BorcDetaylari", "ns2"),
        ArrayOfBorcDetayi.class,
        arrayOfBorcDetayi
    );

    requestTahsilat.setAdSoyad(adSoyad);
    requestTahsilat.setBorcDetaylari(borcDetayiJAXBElement);
    requestTahsilat.setKentliId(new BigDecimal(billDTO.getInfo7()));
    requestTahsilat.setReferansNo(referansNoElement);
    requestTahsilat.setKimlikNo(identityNoElement);
    requestTahsilat.setTahsilatTarihi(tahsilatTarihiElement);
    requestTahsilat.setTarih(xmlGregorianCalendar);
    requestTahsilat.setToplamTutar(billDTO.getBillAmount());

    try {
        // JAXBContext ve Marshaller oluşturulması
        JAXBContext jaxbContext = JAXBContext.newInstance(BorcTahsilati.class);
        Marshaller marshaller = jaxbContext.createMarshaller();
        
        // Marshaller özellikleri
        marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, Boolean.TRUE);
        marshaller.setProperty(Marshaller.JAXB_SCHEMA_LOCATION, "http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts");

        // Marshaller ile request'i yazdırma
        marshaller.marshal(requestTahsilat, System.out);

    } catch (Exception e) {
        e.printStackTrace();
    }

    // Servis çağrısı
    Holder<BigDecimal> kentliBorclariniOdeResult = new Holder<>();
    Holder<Sonuc> sonuc = new Holder<>();
    
    getSeferihisarService().kentliBorclariniOde(requestTahsilat, kentliBorclariniOdeResult, sonuc);
    String institutionCodeControl = sonuc.value.getKod().value();
    String responseInternalResultCode = String.valueOf(sonuc.value.getHataKodu());

    resolveResponseMessage(responseInternalResultCode, response);

    if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus()) && "Basarili".equals(institutionCodeControl)) {
        billDTO.setInfo9(kentliBorclariniOdeResult.value.toString());
        response.setBillDTO(billDTO);
    }

    response.setInstitutionResultDetail(sonuc.value.getMesaj().getValue());
    setRemoteResponseData(response, remoteRequest, PYMLogUtil.convertObjectToJsonString(requestTahsilat), PYMLogUtil.convertObjectToJsonString(kentliBorclariniOdeResult));

    return response;
}
