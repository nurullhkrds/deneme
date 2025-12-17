    @Override
    public GetOrderedBillsDetailResponse getOrderedBillsDetail(GetOrderedBillsDetailRequest request) throws MicroException {
        GetOrderedBillsDetailResponse response = new GetOrderedBillsDetailResponse();

        GetOrderedBillsDetailCriteria criteria = new GetOrderedBillsDetailCriteria();
        criteria.setOrderId(request.getOrderId());
        criteria.setCustomerNo(request.getCustomerNo());
        criteria.setSubscriberNo(request.getSubscriberNo());
        criteria.setProductCode(request.getProductCode());
        criteria.setInstitutionCode(request.getInstitutionCode());

        String sqlForPaymentOrderInfo = GetOrderedBillsDetailTransformer.preparePaymentOrderSqlStatement(criteria);
        String sqlForPaymentOrderAnalysis = GetOrderedBillsDetailTransformer.prepareSqlPaymentOrderAnalysisStatement(criteria);

        MapSqlParameterSource parameterSource = GetOrderedBillsDetailTransformer.prepareQueryParameters(criteria);

        Map<String, Object> paymentOrderInfoRecord = jdbcTemplate.queryForMap(sqlForPaymentOrderInfo, parameterSource);
        List<Map<String, Object>> paymentOrderAnalysisRecords = jdbcTemplate.queryForList(sqlForPaymentOrderAnalysis, parameterSource);

        OrderInfoWebDTO paymentOrderInfoDTO = paymentOrderDataMapper.toOrderInfoWebDTO(paymentOrderInfoRecord);

        response = paymentOrderDataMapper.toOrderedBillsDetail(paymentOrderAnalysisRecords,criteria.getFlag());
        response.setOrderInfoDTO(paymentOrderInfoDTO);



        return response;
    }




    
    default GetOrderedBillsDetailResponse toOrderedBillsDetail(List<Map<String, Object>> records, String flag) {
        GetOrderedBillsDetailResponse response = new GetOrderedBillsDetailResponse();
        List<PaymentAnalysisWebDTO> paymentAnalysisDTOList = new ArrayList<>();
        List<NotPaidBillInfoWebDTO> notPaidBillInfoWebList = new ArrayList<>();
        List<PayableBillInfoWebDTO> payableBillInfoDTOList = new ArrayList<>();

        if (EnumBusinessFlagType.YFD.getValue().equals(flag)) {
            if (records == null || records.isEmpty()) {
                int currentMonth = LocalDate.now().getMonthValue();
                for (int i = 0; i < 5; i++) {
                    String strCurrentMonth = mod(currentMonth, i) + "";
                    PaymentAnalysisWebDTO dto = new PaymentAnalysisWebDTO();
                    dto.setAmount(BigDecimal.ZERO);
                    dto.setBillCount(0);
                    dto.setCurrencyCode("");
                    dto.setMonth(strCurrentMonth);
                    dto.setRateOfChangePreMonth(null);
                    if (currentMonth - Integer.parseInt(strCurrentMonth) < 0) {
                        Calendar prevYear = Calendar.getInstance();
                        prevYear.add(Calendar.YEAR, -1);
                        dto.setYear(prevYear.get(Calendar.YEAR) + "");
                    } else {
                        dto.setYear(Calendar.getInstance().get(Calendar.YEAR) + "");
                    }

                    paymentAnalysisDTOList.add(dto);
                }
                paymentAnalysisDTOList.sort(new Comparator<PaymentAnalysisWebDTO>() {
                    @Override
                    public int compare(PaymentAnalysisWebDTO o1, PaymentAnalysisWebDTO o2) {
                        Integer firstYear = Integer.valueOf(o1.getYear());
                        Integer secondYear = Integer.valueOf(o2.getYear());

                        Integer firstMounth = Integer.valueOf(o1.getMonth());
                        Integer secondMount = Integer.valueOf(o2.getMonth());

                        int c;
                        c = firstYear.compareTo(secondYear);
                        if (c == 0) {
                            c = firstMounth.compareTo(secondMount);
                        }

                        return c;

                    }

                });

                response.setPaymentAnalysisDTOList(paymentAnalysisDTOList);
                response.setNotPaidBillInfoWebList(notPaidBillInfoWebList);
                response.setPayableBillInfoDTOList(payableBillInfoDTOList);
                return response;
            }

            for (Map<String, Object> record : records) {
                processOrderedBillsDetailListDTO(records, paymentAnalysisDTOList, notPaidBillInfoWebList, payableBillInfoDTOList, record,flag);
            }


        }

        return null;
    }


    default void processOrderedBillsDetailListDTO(List<Map<String, Object>> records,
                                                  List<PaymentAnalysisWebDTO> paymentAnalysisDTOList,
                                                  List<NotPaidBillInfoWebDTO> notPaidBillInfoDTOList,
                                                  List<PayableBillInfoWebDTO> payableBillInfoDTOList,
                                                  Map<String, Object> record,String flag) {

        LocalDate date = LocalDate.now();
        int curentMonth = date.getMonthValue();

        int paidTotalCount = (int) record.get("TOTAL_PAID_COUNT");
        int payableTotalCount = (int) record.get("TOTAL_PAYABLE_COUNT");
        int notPaidTotalCount = (int) record.get("TOTAL_NOT_PAID_COUNT");

        String currency = (String) record.get("CURRENCY");
        BigDecimal paidTotalAmount = (BigDecimal) record.get("TOTAL_PAID_AMOUNT");
        BigDecimal payableTotalAmount = (BigDecimal) record.get("TOTAL_PAYABLE_AMOUNT");
        BigDecimal notPaidTotalAmount = (BigDecimal) record.get("TOTAL_NOT_PAID_AMOUNT");

        if (record.get("LAST_BILL_DUE_DATE") == null) {
            return;
        }
        LocalDate billDueDate = DateUtils.convertTimeStampToLocalDate(record.get("LAST_BILL_DUE_DATE"), DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN);
        int billDueDateMonth = billDueDate.getMonthValue();
        if (curentMonth != billDueDateMonth && mod(curentMonth, billDueDateMonth) > 4) {
            return;
        }
        if (EnumBusinessFlagType.YFD.getValue().equals(flag)) {
            PaymentAnalysisWebDTO paymentAnalysisWebDTO = new PaymentAnalysisWebDTO();

            if (billDueDateMonth == curentMonth) {
                if (payableTotalAmount == null) {
                    paymentAnalysisWebDTO.setAmount(paidTotalAmount);
                } else if (paidTotalAmount == null)
                    paymentAnalysisWebDTO.setAmount(payableTotalAmount);
                else {
                    paymentAnalysisWebDTO.setAmount(paidTotalAmount.add(payableTotalAmount));
                }

            } else {
                paymentAnalysisWebDTO.setAmount(paidTotalAmount);
            }
            paymentAnalysisWebDTO.setMonth(billDueDate + "");
            int currentIndex = records.indexOf(record);

            Map<String, Object> previous = records.get(currentIndex - 1);
            paymentAnalysisWebDTO.setRateOfChangePreMonth(calculateRateOfChangePreMonth(previous, record));
            paymentAnalysisWebDTO.setCurrencyCode(currency);

            if (paidTotalCount != 0) {
                if (paidTotalCount == 1) {
                    paymentAnalysisWebDTO.setReceiptCode((String) record.get("PAYMENT_RECEIPT_CODE"));
                    paymentAnalysisWebDTO.setAccountingContractNumber((String) record.get("PAYMENT_CONTRACT_NO"));
                }
            } else {
                paymentAnalysisWebDTO.setBillCount(0);
                if (paidTotalAmount == null) {
                    paymentAnalysisWebDTO.setAmount(BigDecimal.ZERO);
                }
            }
            paymentAnalysisDTOList.add(paymentAnalysisWebDTO);


            if (payableTotalCount != 0) {
                PayableBillInfoWebDTO payableBillInfoDTO = new PayableBillInfoWebDTO();
                if (payableTotalCount == 1) {
                    payableBillInfoDTO.setDuePaymentDate(DateUtils.convertTimeStampToString(record.get("LAST_BILL_DUE_DATE"), DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN));
                }
                payableBillInfoDTO.setBillCount(payableTotalCount);
                payableBillInfoDTO.setTotalAmount(payableTotalAmount);
                payableBillInfoDTO.setMonth(billDueDateMonth + "");
                payableBillInfoDTO.setYear(billDueDate.getYear() + "");
                payableBillInfoDTO.setCurrencyCode(currency);
                payableBillInfoDTOList.add(payableBillInfoDTO);
            }

            if (notPaidTotalCount != 0) {
                NotPaidBillInfoWebDTO notPaidInfoDTO = new NotPaidBillInfoWebDTO();
                if (notPaidTotalCount == 1) {
                    notPaidInfoDTO.setDuePaymentDate(DateUtils.convertTimeStampToString(record.get("LAST_BILL_DUE_DATE"), DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN));
                }
                notPaidInfoDTO.setBillCount(notPaidTotalCount);
                notPaidInfoDTO.setTotalAmount(notPaidTotalAmount);
                notPaidInfoDTO.setMonth(billDueDateMonth + "");
                notPaidInfoDTO.setYear(billDueDate.getYear() + "");
                notPaidInfoDTO.setCurrencyCode(currency);
                notPaidBillInfoDTOList.add(notPaidInfoDTO);
            }

        }
        else if (EnumBusinessFlagType.MY_HOME.getValue().equals(flag)){
            EnumBillStatus paymentStatus = EnumBillStatus.parse((String)record.get("PAYMENT_STATUS"));

            PaymentAnalysisWebDTO paymentAnalysisWebDTO = new PaymentAnalysisWebDTO();
            paymentAnalysisWebDTO.setPaymentDate(DateUtils.convertTimeStampToLocalDate(record.get("PAYMENT_DATE"),DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN) );

            if (payableTotalCount == 1){
                paymentAnalysisWebDTO.setAmount(payableTotalAmount);
            }
            else if (paidTotalCount == 1){
                paymentAnalysisWebDTO.setAmount(paidTotalAmount);

            }

            int currentIndex = records.indexOf(record);
            Map<String, Object> previous =null;
            if (currentIndex > 0){
                previous = records.get(currentIndex - 1);
            }

            paymentAnalysisWebDTO.setRateOfChangePreMonth(calculateRateOfChangePrePayment(previous, record));
            paymentAnalysisWebDTO.setCurrencyCode(currency);
            paymentAnalysisWebDTO.setStatus(paymentStatus);

            if (paidTotalCount== 1) {
                paymentAnalysisWebDTO.setReceiptCode((String) record.get("PAYMENT_RECEIPT_CODE"));
                paymentAnalysisWebDTO.setAccountingContractNumber((String) record.get("PAYMENT_CONTRACT_NO"));
            }

            else if(paidTotalCount == 0 && payableTotalCount == 0){
                paymentAnalysisWebDTO.setBillCount(0);
                paymentAnalysisWebDTO.setAmount(BigDecimal.ZERO);
            }
            paymentAnalysisDTOList.add(paymentAnalysisWebDTO);


            if (payableTotalCount == 1) {
                PayableBillInfoWebDTO payableBillInfoWebDTO = new PayableBillInfoWebDTO();
                payableBillInfoWebDTO.setDuePaymentDate(DateUtils.convertTimeStampToString(record.get("LAST_BILL_DUE_DATE"), DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN));

                payableBillInfoWebDTO.setBillCount(payableTotalCount);
                payableBillInfoWebDTO.setTotalAmount(payableTotalAmount);
                payableBillInfoWebDTO.setCurrencyCode(currency);

                payableBillInfoDTOList.add(payableBillInfoWebDTO);

            }

            if (notPaidTotalCount != 0) {
                NotPaidBillInfoWebDTO notPaidBillInfoWebDTO = new NotPaidBillInfoWebDTO();
                if (notPaidTotalCount == 1) {
                    notPaidBillInfoWebDTO.setDuePaymentDate(DateUtils.convertTimeStampToString(record.get("LAST_BILL_DUE_DATE"), DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN));
                    notPaidBillInfoWebDTO.setBillNo((String) record.get("BILL_NO"));

                }
                if (notPaidBillInfoDTOList.isEmpty()) {
                    notPaidBillInfoWebDTO.setBillCount(notPaidTotalCount);
                    notPaidBillInfoWebDTO.setTotalAmount(notPaidTotalAmount);
                    notPaidBillInfoWebDTO.setCurrencyCode(currency);
                    notPaidBillInfoDTOList.add(notPaidBillInfoWebDTO);
                }
            }

        }





    }

    private Double calculateRateOfChangePrePayment(Map<String, Object> previous, Map<String, Object> current){

        if (previous == null) {
            return null;
        }

        EnumBillStatus currentPaidStatus = EnumBillStatus.parse((String)current.get("PAYMENT_STATUS"));
        BigDecimal currentPaidTotalAmount = (BigDecimal) current.get("TOTAL_PAID_AMOUNT");
        BigDecimal currentPayableTotalAmount = (BigDecimal) current.get("TOTAL_PAYABLE_AMOUNT");

        EnumBillStatus previousPaidStatus = EnumBillStatus.parse((String)previous.get("PAYMENT_STATUS"));
        BigDecimal previousPaidTotalAmount = (BigDecimal) previous.get("TOTAL_PAID_AMOUNT");
        BigDecimal previousPayableTotalAmount = (BigDecimal) previous.get("TOTAL_PAYABLE_AMOUNT");


        BigDecimal lastPaymentAmount = currentPaidStatus.equals(EnumBillStatus.PAID) ? currentPaidTotalAmount : currentPayableTotalAmount;
        BigDecimal prePaymentAmount = previousPaidStatus.equals(EnumBillStatus.PAID) ? previousPaidTotalAmount : previousPayableTotalAmount;

        if (lastPaymentAmount == null || prePaymentAmount == null || prePaymentAmount.compareTo(BigDecimal.valueOf(0d)) == 0) {
            return null;
        }

        BigDecimal rate = lastPaymentAmount.subtract(prePaymentAmount).divide(prePaymentAmount, 4, RoundingMode.HALF_EVEN);
        return rate.multiply(BigDecimal.valueOf(100.0d)).doubleValue();
    }

    private Double calculateRateOfChangePreMonth(Map<String, Object> previous, Map<String, Object> current) {
        if (previous == null) {
            return null;
        }

        int monthNow = LocalDate.now().getMonthValue();
        BigDecimal currentPaymentAmount = (BigDecimal) current.get("TOTAL_PAID_AMOUNT");
        BigDecimal prePaymentAmount = (BigDecimal) previous.get("TOTAL_PAID_AMOUNT");

        BigDecimal currentPayableTotalAmount = (BigDecimal) current.get("TOTAL_PAYABLE_AMOUNT");

        int currentBillDueDate = DateUtils.convertTimeStampToLocalDate(current.get("LAST_BILL_DUE_DATE"), DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN).getMonthValue();
        int preBillDueDate = DateUtils.convertTimeStampToLocalDate(previous.get("LAST_BILL_DUE_DATE"), DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN).getMonthValue();


        if (mod(currentBillDueDate, preBillDueDate) != 1) {
            return null;
        }

        if (currentBillDueDate == monthNow) {
            if (currentPayableTotalAmount != null) {
                if (currentPaymentAmount == null) {
                    currentPaymentAmount = currentPayableTotalAmount;
                } else {
                    currentPaymentAmount = currentPaymentAmount.add(currentPaymentAmount);
                }
            }
        }

        if (currentPayableTotalAmount == null || prePaymentAmount == null || prePaymentAmount.compareTo(BigDecimal.valueOf(0d)) == 0) {
            return null;
        }

        BigDecimal rate = currentPaymentAmount.subtract(prePaymentAmount).divide(prePaymentAmount, 4, RoundingMode.HALF_EVEN);
        return rate.multiply(BigDecimal.valueOf(100.0d)).doubleValue();

    }


    private int mod(Integer lastPaymentMount, Integer prePaymentMount) {
        int value = lastPaymentMount - prePaymentMount;
        if (value <= 0) {
            value += 12;
        }

        return value;
    }
