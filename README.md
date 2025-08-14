@Override
public QueryBillsResponse queryBills(QueryBillsRequest remoteRequest) {
    QueryBillsResponse response = new QueryBillsResponse();
    List<BaseBillIDTO> billDTOS = new ArrayList<>();

    // 1. getBorcVermeHizmetOdemePlaniDTOs
    if (institutionResponse.getBorcVermeHizmetOdemePlaniDTOs() != null &&
        !institutionResponse.getBorcVermeHizmetOdemePlaniDTOs().getOdemePlaniIDTOs().isEmpty()) {

        for (OdemePlaniIDTO odemePlaniDTO : institutionResponse.getBorcVermeHizmetOdemePlaniDTOs().getOdemePlaniIDTOs()) {
            BaseBillIDTO billDTO = new BaseBillIDTO();
            fillCommonFields(billDTO, remoteRequest, institutionResponse);
            billDTO.setBillNo(formatBillNo(odemePlaniDTO));
            billDTO.setInfo1(odemePlaniDTO.getTahsilatTuru() + "-");
            billDTO.setBillAmount(odemePlaniDTO.getTutar());
            billDTOS.add(billDTO);
        }
    }

    // 2. getKonutKredisiOdemePlanIDTOs
    if (institutionResponse.getKonutKredisiOdemePlanIDTOs() != null &&
        !institutionResponse.getKonutKredisiOdemePlanIDTOs().getOdemePlaniIDTOs().isEmpty()) {

        for (OdemePlaniIDTO odemePlaniDTO : institutionResponse.getKonutKredisiOdemePlanIDTOs().getOdemePlaniIDTOs()) {
            BaseBillIDTO billDTO = new BaseBillIDTO();
            fillCommonFields(billDTO, remoteRequest, institutionResponse);
            billDTO.setBillNo(formatBillNo(odemePlaniDTO));
            billDTO.setInfo1(odemePlaniDTO.getTahsilatTuru() + "-");
            billDTO.setBillAmount(odemePlaniDTO.getTutar());
            billDTOS.add(billDTO);
        }
    }

    // 3. EMS üyesi mi? (tek standart fatura)
    if (institutionResponse.isEmsUyesiMi()) {
        billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
    }

    // 4. EMS üyesi + rezerv ödeme kontrolü (güncel iş kuralı)
    if (institutionResponse.isEmsUyesiMi() && institutionResponse.getEmsRezervOdemeleriIDTO() != null) {
        EmsRezervOdemeleriIDTO ems = institutionResponse.getEmsRezervOdemeleriIDTO();

        // 1. Pay Artırım Tutari
        if (ems.getBirinciPayArtirimTutari() != null) {
            billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
        }
        // 2. Pay Artırım Tutari
        if (ems.getIkinciPayArtirimTutari() != null) {
            billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
        }
        // 3. Pay Artırım Tutari
        if (ems.getUcuncuPayArtirimTutari() != null) {
            billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
        }
        // Rezerv Artırım Bilgisi
        if (Boolean.TRUE.equals(ems.isRezervArtirimBilgisiVarMi())) {
            billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
        }
    }

    response.setBills(billDTOS);
    return response;
}

/**
 * Ortak alanları setler.
 * Bazı if bloklarında çağırmayabilirsin.
 */
private void fillCommonFields(BaseBillIDTO billDTO, QueryBillsRequest req, InstitutionResponse inst) {
    billDTO.setProduct(req.getProduct());
    billDTO.setInstitution(req.getInstitution());
    billDTO.setSubscriberNo(req.getMergedSubscriberNumber());
    billDTO.setBillIssueDate(LocalDate.now());
    billDTO.setSubscriberName(inst.getAdi() + inst.getSoyadi());
    billDTO.setInstitutionServiceType(req.getInstitutionServiceType());
    billDTO.setIsDeclarationPayable(false);
    billDTO.setCurrency(req.getCurrencyCode());
}

/**
 * Plan bazlı fatura numarası formatlama.
 */
private String formatBillNo(OdemePlaniIDTO plan) {
    return plan.getOdeyiciID() + plan.getKrediId() + plan.getTahsilatTuru();
}

/**
 * EMS için standart fatura oluşturur.
 */
private BaseBillIDTO newEmsBill(QueryBillsRequest req, InstitutionResponse inst) {
    BaseBillIDTO dto = new BaseBillIDTO();
    fillCommonFields(dto, req, inst);
    dto.setBillNo("standdan alınacak");
    dto.setBillAmount(BigDecimal.ZERO);
    // dto.setInfo1("-");
    return dto;
}
