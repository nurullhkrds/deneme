 @Test
    void givenFetchPaymentOrderedBillsRequest_whenFetchPaymentOrderedBills_thenReturnFetchPaymentOrderedBillsResponse() throws InvocationTargetException, NoSuchMethodException, IllegalAccessException {
        FetchPaymentOrderedBillsRequest remoteRequest = new FetchPaymentOrderedBillsRequest();
        remoteRequest.setInstitution("ARMADAŞ");
        remoteRequest.setProduct("DOĞALGAZ");
        remoteRequest.setDueDate(LocalDate.of(2024,11,15));
        insertReturnMap();
        insertParams("OTLYKB");

        FetchPaymentOrderedBillsResponse response = testFetchPaymentOrderedBills(BankaService.class, remoteRequest);
        assertEquals(3, response.getBills().size());
    }



if (aboneBorc != null && aboneBorc.getBelgeNo() != null && isDueDateValid(aboneBorc.getSonOdemeTarih())) {


    private boolean isDueDateValid(XMLGregorianCalendar dueDate) {
        if (dueDate == null) {
            return false;
        }
        LocalDate todayDate = LocalDate.now();
        LocalDate dueLocalDate = dueDate.toGregorianCalendar().toZonedDateTime().toLocalDate();
        return !dueLocalDate.isBefore(todayDate);
    }
