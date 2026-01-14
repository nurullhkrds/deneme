public GetOrderedBillsDetailResponse getOrderedBillsDetail(GetOrderedBillsDetailRequest request) {

    GetOrderedBillsDetailResponse microResponse =
            paymentOrderDataService.getOrderedBillsDetail(request);

    GetOrderedBillsDetailResponse response =
            billPaymentOrderRestService.getOrderedBillsDetail(request);

    return mergeResponses(microResponse, response);
}

private GetOrderedBillsDetailResponse mergeResponses(GetOrderedBillsDetailResponse micro,
                                                    GetOrderedBillsDetailResponse other) {

    if (micro == null) return other;
    if (other == null) return micro;

    GetOrderedBillsDetailResponse merged = new GetOrderedBillsDetailResponse();

    // 1) orderInfoDTO: hangisi doluysa onu al, ikisi doluysa "micro" öncelikli yap (istersen tersini yaparsın)
    merged.setOrderInfoDTO(firstNonNull(micro.getOrderInfoDTO(), other.getOrderInfoDTO()));

    // 2) Listeleri birleştir (null-safe)
    merged.setPaymentAnalysisDTOList(mergeList(micro.getPaymentAnalysisDTOList(), other.getPaymentAnalysisDTOList()));
    merged.setNotPaidBillInfoWebList(mergeList(micro.getNotPaidBillInfoWebList(), other.getNotPaidBillInfoWebList()));
    merged.setPayableBillInfoDTOList(mergeList(micro.getPayableBillInfoDTOList(), other.getPayableBillInfoDTOList()));

    return merged;
}

private <T> List<T> mergeList(List<T> a, List<T> b) {
    List<T> out = new ArrayList<>();
    if (a != null) out.addAll(a);
    if (b != null) out.addAll(b);
    return out;
}

private <T> T firstNonNull(T a, T b) {
    return a != null ? a : b;
}
