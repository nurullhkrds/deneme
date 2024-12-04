 public static <T> JAXBElement<T> createJAXBElement(String localPart, T value) {
        QName qname = new QName("", localPart);  // Namespace URI olarak boş string kullanılıyor
        return new JAXBElement<>(qname, (Class<T>) value.getClass(), value);
    }
