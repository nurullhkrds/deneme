LocalDate localDate = billDTO.getPaymentInformation().getPaymentDate();
GregorianCalendar gregorianCalendar = GregorianCalendar.from(localDate.atStartOfDay(ZoneId.systemDefault()));
XMLGregorianCalendar xmlGregorianCalendar = DatatypeFactory.newInstance().newXMLGregorianCalendar(gregorianCalendar);

JAXBElement<XMLGregorianCalendar> tahsilatTarihi = new JAXBElement<>(
        new QName("tahsilatTarihi"),
        XMLGregorianCalendar.class,
        xmlGregorianCalendar
);
