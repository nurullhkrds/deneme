if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus())) {
            List<TahsilatBilgisi> tahsilatBilgisi = (mutabakatDetayResult.value).getTahsilatBilgisi();
            List<ReconciliationRecordDTO> paymentList = tahsilatBilgisi.stream().flatMap((tahsilat) -> {
                return (tahsilat.getBorcDetaylari().getValue()).getBorcDetayi().stream().map((tahsilatDetail) -> {
                    String billNo = generateBillNo(tahsilatDetail.getSistemId(), tahsilatDetail.getHesapId(), tahsilatDetail.getBeyanAnaId(), tahsilatDetail.getBeyanSiraNo(), tahsilatDetail.getYil(), tahsilatDetail.getTaksit(), tahsilatDetail.getVadeTarihi());
                    ReconciliationRecordDTO recordDTO = new ReconciliationRecordDTO();
                    recordDTO.setInfo1(String.valueOf(tahsilatDetail.getSistemId()));
                    recordDTO.setInfo2(String.valueOf(tahsilatDetail.getHesapId()));
                    recordDTO.setInfo3(String.valueOf(tahsilatDetail.getBeyanAnaId()));
                    recordDTO.setInfo4(String.valueOf(tahsilatDetail.getBeyanSiraNo()));
                    recordDTO.setInfo5(String.valueOf(tahsilatDetail.getYil()));
                    recordDTO.setInfo6(String.valueOf(tahsilatDetail.getTaksit()));
                    recordDTO.setInfo7(String.valueOf(tahsilat.getKentliId().getValue()));
                    recordDTO.setBillNo(billNo);
                    recordDTO.setInfo9(String.valueOf(tahsilat.getSiparisNo().getValue()));
                    recordDTO.setBillNo(String.valueOf(tahsilatDetail.getBeyanAnaId()));
                    recordDTO.setSubscriberName(tahsilat.getAdSoyad().getValue());
                    recordDTO.setInfo8(String.valueOf(tahsilat.getKimlikNo().getValue()));
                    recordDTO.setProduct(remoteRequest.getProduct());
                    recordDTO.setInstitution(remoteRequest.getInstitution());
                    recordDTO.setOperationDate(remoteRequest.getRequestDate().toLocalDate());
                    recordDTO.setReconciliationDate(remoteRequest.getReconciliationDate());
                    recordDTO.setPaymentAmount(tahsilatDetail.getToplamTutar().getValue());
                    return recordDTO;
                });
            }).toList();
