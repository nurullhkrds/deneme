  @Test
    public void testDeleteReturnMaps_CatchBlock() {
        // Arrange
        DeleteIdsRequest request = new DeleteIdsRequest();
        request.setIds(Arrays.asList(1L, 2L, 3L)); // Test için bazı ID'ler

        // Mock the repository to throw an exception
        Mockito.doThrow(new RuntimeException("Mock exception")).when(returnMapRepository).findAllById(request.getIds());

        // Act
        Result result = returnMapService.deleteReturnMaps(request);

        // Assert
        assertFalse(result.isSuccess()); // Sonucun hata olduğunu doğrula
        assertEquals(400, result.getStatusCode()); // Hata kodunun 400 olduğunu doğrula
        assertTrue(result.getMessage().contains("Error ! Mock exception")); // Hata mesajının doğru olduğunu kontrol et
    }
