 if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus())) {
            ArrayList<ReconciliationRecordDTO> paymentList = new ArrayList<>();

            List<TahsilatBilgisi> tahsilatBilgisi=mutabakatDetayResult.value.getTahsilatBilgisi();
            tahsilatBilgisi.stream().map(info->{
                ReconciliationRecordDTO paymentRecord = new ReconciliationRecordDTO();
                paymentRecord.setProduct(remoteRequest.getProduct());
                paymentRecord.setInstitution(remoteRequest.getInstitution());
    


            })


        }


	for (ReadAllMutabakatByDateL mutabakatDetail : arrayOfMutabakatByDateL.getReadAllMutabakatByDateL()) {
					ReconciliationRecordDTO paymentRecord = new ReconciliationRecordDTO();
					paymentRecord.setProduct(remoteRequest.getProduct());
					paymentRecord.setInstitution(remoteRequest.getInstitution());
					paymentRecord.setSubscriberNo(StringUtils.leftPad(mutabakatDetail.getSicilNo(), 10, '0'));
					paymentRecord.setSubscriberName(mutabakatDetail.getAd());
					paymentRecord.setBillNo(mutabakatDetail.getMakbuzNo());
					paymentRecord
							.setPaymentAmount(new BigDecimal(mutabakatDetail.getTahsilatToplam().replace(',', '.')));
					paymentRecord.setReconciliationDate(remoteRequest.getReconciliationDate());
					paymentRecord.setOperationDate(remoteRequest.getRequestDate().toLocalDate());

					paymentList.add(paymentRecord);
				}
