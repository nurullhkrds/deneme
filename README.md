    // Ürün nesnesini String'e dönüştüren bir map metodu ekleyin
    default String map(Product product) {
        return product != null ? product.getName() : null; // Ürün adını döndüren basit bir dönüşüm
    }

    // Eğer ters dönüşüm de gerekliyse:
    default Product map(String productName) {
        if (productName == null || productName.isEmpty()) {
            return null;
        }
        Product product = new Product();
        product.setName(productName);
        return product;
    }
