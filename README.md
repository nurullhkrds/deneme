 @Test
    void shouldThrowExceptionWhenInstantiatingLogUtils() {
        assertThrows(IllegalStateException.class, () -> {
            // LogUtils sınıfının yapıcısını çağırarak bir örneğini oluşturmaya çalışıyoruz
            // Bu, IllegalStateException fırlatmalıdır
            new LogUtils();
        });
    }
