        if (createAccountingDTO.getResponseCommissionInformation()!= null) {

            ResponseCommissionInformation responseCommissionInformation = createAccountingDTO.getResponseCommissionInformation();
            for (CommissionOutputAccountingApiDTO commissionOutputDTO : responseCommissionInformation.getCommissionOutputAccountingApiDTOList()) {
                MakeProvisionInnerDTO commissionProvisionInnerRequest = new MakeProvisionInnerDTO();
                commissionProvisionInnerRequest.setAccountNo(accountPaymentMethodDetailDTO.getAccountNo());
                commissionProvisionInnerRequest.setAmount(commissionOutputDTO.getAmount());
                commissionProvisionInnerRequest.setCurrency(commissionOutputDTO.getCurrency());
                commissionProvisionInnerRequest.setDescription(commissionOutputDTO.getDescription());
                if (Boolean.TRUE.equals(commissionOutputDTO.getIsCommissionTax())) {
                    commissionProvisionInnerRequest.setCommissionTax(true);
                } else {
                    commissionProvisionInnerRequest.setCommission(true);
                }
                commissionProvisionInnerRequest.setProvisionCode(commissionOutputDTO.getProvisionCode());
                commissionProvisionInnerRequest.setReservationId(commissionOutputDTO.getReservationId());
                commissionProvisionInnerRequest.setCommissionBalanceType(commissionOutputDTO.getBalanceControlType());
                commissionProvisionInnerRequest.setFeeDefinitionId(commissionOutputDTO.getFeeDefinitionId());
                commissionProvisionInnerRequest.setFeeDefinitionId(commissionOutputDTO.getFeeDefinitionId());
                commissionProvisionInnerRequest.setDelinquencyRequired(commissionOutputDTO.getIsDelinquencyRequired());
                makeProvisionInnerList.add(commissionProvisionInnerRequest);
            }
        }
