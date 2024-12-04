  private ArrayList<ReconciliationRecordDTO> getReconciliationRecordDTOList(ResultOfListOfAboneTahsilat wsResponse, DoPaymentDetailReconciliationRequest i_remoteRequestDTO ) {

        ArrayList<ReconciliationRecordDTO> reconciliationRecordDTOList = new ArrayList<>();
        ReconciliationRecordDTO reconRecord ;

        if( wsResponse.getKayit() != null
                && wsResponse.getKayit().getValue().getAboneTahsilat() != null
                && !wsResponse.getKayit().getValue().getAboneTahsilat().isEmpty()) {
            for( int i = 0; i < wsResponse.getKayit().getValue().getAboneTahsilat().size(); i++ ) {
                AboneTahsilat aboneTahsilat = wsResponse.getKayit().getValue().getAboneTahsilat().get( i );
                reconRecord = new ReconciliationRecordDTO();
                reconRecord.setProduct( i_remoteRequestDTO.getProduct() );
                reconRecord.setInstitution( i_remoteRequestDTO.getInstitution() );
                reconRecord.setReconciliationDate( i_remoteRequestDTO.getReconciliationDate() );
                reconRecord.setOperation( EnumReconciliationOperation.PAYMENT.getValue() );
                reconRecord.setOperationDate(LocalDate.from(i_remoteRequestDTO.getRequestDate()));
                reconRecord.setSubscriberNo( aboneTahsilat.getAboneNo() != null ? aboneTahsilat.getAboneNo().toString() : null );
                reconRecord.setBillNo( aboneTahsilat.getMakbuzNo() != null && aboneTahsilat.getMakbuzNo().getValue().length() > 50 ? aboneTahsilat.getMakbuzNo().getValue().substring( 0, 50 ) : String.valueOf(aboneTahsilat.getMakbuzNo()));
                reconRecord.setPaymentAmount( aboneTahsilat.getTutar() );

                reconciliationRecordDTOList.add( reconRecord );
            }

        }
        return reconciliationRecordDTOList;
    }
