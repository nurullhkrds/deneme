@Query("SELECT r FROM ReturnMapDefinition r WHERE LOWER(r.returnMapCode) = LOWER(:returnMapCode)")
Optional<ReturnMapDefinition> findByReturnMapCode(@Param("returnMapCode") String returnMapCode);
