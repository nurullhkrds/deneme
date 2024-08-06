java.lang.NullPointerException: Cannot invoke "java.lang.Integer.intValue()" because the return value of "java.lang.reflect.Method.invoke(Object, Object[])" is null
   @Test
    void testGetBlockDayCount() throws Exception {
        InstitutionChannelPymMethodDTO methodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnnlPymMthdPscDTO pscDTO = new InstitutionChnnlPymMthdPscDTO();
        methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);

        Method method = CardProvisionServiceImpl.class.getDeclaredMethod("getBlockDayCount", InstitutionChannelPymMethodDTO.class, InstitutionChnnlPymMthdPscDTO.class);
        method.setAccessible(true);
        int blockDayCount = (int) method.invoke(cardProvisionServiceImpl, methodDTO, pscDTO);

        assertEquals(3, blockDayCount);
    }
