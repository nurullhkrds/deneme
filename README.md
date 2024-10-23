 public static <T> JAXBElement<T> toJAXBElement(String name, Class<T> clazz, T value) {
        return new JAXBElement<>(
            new QName(name),
            clazz,
            value
        );
    }
