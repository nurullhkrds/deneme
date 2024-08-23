    @Query("SELECT r FROM ReturnMapDefinition r WHERE r.returnMapCode = :returnMapCode")
    Optional<ReturnMapDefinition> findByReturnMapCode(@Param("returnMapCode") String returnMapCode);
