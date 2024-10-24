if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus())) {
            ArrayList<ReconciliationRecordDTO> paymentList = new ArrayList<>();

            List<TahsilatBilgisi> tahsilatBilgisi = mutabakatDetayResult.value.getTahsilatBilgisi();
            
            for (TahsilatBilgisi info : tahsilatBilgisi) {
                info.getBorcDetaylari().getValue().getBorcDetayi().stream().map(borcDetayi -> {
                    ReconciliationRecordDTO paymentRecord = new ReconciliationRecordDTO();
                    paymentRecord.setProduct(remoteRequest.getProduct());
                    paymentRecord.setInstitution(remoteRequest.getInstitution());
                    paymentRecord.setSubscriberName(String.valueOf(info.getAdSoyad().getValue()));
                    paymentRecord.setBillNo(String.valueOf(info.getSiparisNo().getValue()));
                    paymentRecord.setPaymentAmount(info.getToplamTutar().getValue());
                    paymentRecord.setInfo3(String.valueOf(info.getKentliId().getValue()));

                    paymentRecord.setInfo1(String.valueOf(info.getBelgeNo().getValue()));
                    paymentRecord.setInfo9(String.valueOf(info.getSiparisNo().getValue()));
                    paymentRecord.setInfo8(String.valueOf(info.getKimlikNo().getValue()));
                    paymentRecord.setReconciliationDate(remoteRequest.getReconciliationDate());
                    paymentRecord.setOperationDate(remoteRequest.getRequestDate().toLocalDate());
                    return paymentRecord;
                }).forEach(paymentList::add);
                ReconciliationRecordDTO paymentRecord = new ReconciliationRecordDTO();
                paymentRecord.setProduct(remoteRequest.getProduct());
                paymentRecord.setInstitution(remoteRequest.getInstitution());
                paymentRecord.setSubscriberName(String.valueOf(info.getAdSoyad().getValue()));
                paymentRecord.setBillNo(String.valueOf(info.getSiparisNo().getValue()));
                paymentRecord.setPaymentAmount(info.getToplamTutar().getValue());
                paymentRecord.setInfo3(String.valueOf(info.getKentliId().getValue()));

                paymentRecord.setInfo1(String.valueOf(info.getBelgeNo().getValue()));
                paymentRecord.setInfo9(String.valueOf(info.getSiparisNo().getValue()));
                paymentRecord.setInfo8(String.valueOf(info.getKimlikNo().getValue()));
                paymentRecord.setReconciliationDate(remoteRequest.getReconciliationDate());
                paymentRecord.setOperationDate(remoteRequest.getRequestDate().toLocalDate());
                paymentList.add(paymentRecord);
            }
