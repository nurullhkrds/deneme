if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus()) || response.getInternalResultCode().equals("621")|| response.getInternalResultCode().equals("620")) {
            response.setTotalPaymentAmount(mutabakatResult.value.getToplamOdemeTutari());
            response.setTotalPaymentCount(mutabakatResult.value.getToplamOdemeSayisi().intValue());
            response.setTotalPaymentCancelAmount(mutabakatResult.value.getIptalOdemeTutari());
            response.setTotalPaymentCancelCount(mutabakatResult.value.getIptalOdemeSayisi().intValue());
        }
