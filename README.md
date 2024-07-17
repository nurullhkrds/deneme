// Mock nesneyi oluşturun
ProvisionNextService provisionNextService = mock(ProvisionNextService.class);

// Metot davranışını belirleyin
when(provisionNextService.makeReverseProvision(any()))
        .thenThrow(new RuntimeException("Test exception"));
