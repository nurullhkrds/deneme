  @Test
    void testGetProvisionType() {
        // SUT (System Under Test)
        CardProvisionServiceImpl cardProvisionServiceImpl = new CardProvisionServiceImpl(
            mock(SwtSwitchIntegrationService.class),
            mock(ProvisionNextService.class),
            mock(AccountingUtilServiceImpl.class),
            mock(AccountingUtil.class)
        );

        // Beklenen değer
        EnumProvisionType expectedProvisionType = EnumProvisionType.CARD;

        // Gerçek değer
        EnumProvisionType actualProvisionType = cardProvisionServiceImpl.getProvisionType();

        // Assert
        assertEquals(expectedProvisionType, actualProvisionType, "Provision type should be CARD");
    }
