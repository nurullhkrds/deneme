String adSoyad = billDTO.getSubscriberName();

// JAXBElement<String> oluştur
JAXBElement<String> adSoyadElement = new JAXBElement<>(
        new QName("adSoyad"),
        String.class,
        adSoyad
);

// requestTahsilat'a set et
requestTahsilat.setAdSoyad(adSoyadElement);
