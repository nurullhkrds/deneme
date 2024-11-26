if (btnName.equals(DCKEY_BTNPG1111PAYMENTCHANNEL)) {// KRMDET
				scope = Scope.FW_INTER_PROCESS_SCOPE;
				cc.put("toCorporateDetail", "1", Scope.FW_CLIENT_PAGE_SCOPE);
				cc.put("detailPageProcessId", "560", Scope.FW_CLIENT_PAGE_SCOPE);
