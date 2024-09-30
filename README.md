    @Query(value = "SELECT icpm FROM InstitutionChannelPymMethod icpm" +
            " WHERE icpm.institutionChannel.id = :channelId" +
            " AND icpm.paymentMethod.code = :paymentMethod" )
    InstitutionChannelPymMethod findFirstByChannelIdAndPaymentMethod(@Param("channelId") Long channelCode,  @Param("paymentMethod") EnumPaymentMethod paymentMethod);
