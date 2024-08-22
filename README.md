@Query("SELECT r FROM ReturnMapDefinition r WHERE r.returnMapCode = :returnMapCode")
    Optional<List<ReturnMapDefinition>> findAllByReturnMapCode(@Param("returnMapCode") String returnMapCode);
