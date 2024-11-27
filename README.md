		BigDecimal fee = (getCommissionInfo(events, dc, cc)!=null)?getCommissionInfo(events, dc, cc).getTotalCommissionAmount():BigDecimal.ZERO;
