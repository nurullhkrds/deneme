   JAXBElement<javax.xml.datatype.XMLGregorianCalendar> tahsilatTarihi = new JAXBElement<>(
                new QName("tahsilatTarihi"),
                javax.xml.datatype.XMLGregorianCalendar.class,
                billDTO.getPaymentInformation().getPaymentDate()
        );

Required type
Provided
declaredType:
Class<T>
Class<XMLGregorianCalendar>
value:
T
LocalDate
