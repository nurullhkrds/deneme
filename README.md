String CITY_PRODUCT_QUERY = "	select s.productCode,"
			+ "       s.productName,"
			+ "       s.cityRelationRequired,"
			+ "       listagg(s.cityName, ',') within group(order by s.cityName asc),"
			+ "       listagg(s.cityCode, ',') within group(order by s.cityName asc)"
			+ "  from (select p.code                   as productCode,"
			+ "               p.name                   as productName,"
			+ "               p.city_relation_required as cityRelationRequired,"
			+ "               city.name                as cityName,"
			+ "               city.code                as cityCode,"
			+ "               inst.id                  as instId"
			+ "          FROM PRODUCT          p,"
			+ "               INSTITUTION      inst,"
			+ "               INSTITUTION_CITY inst_city,"
			+ "               CITY             city"
			+ "         WHERE p.product_family_code = :productFamilyCode"
			+ "           and inst.product_code = p.code"
			+ "           and inst.is_active = 1"
			+ "           and inst_city.institution_id(+) = inst.id"
			+ "           and inst_city.is_active(+) = 1"
			+ "           and city.code(+) = inst_city.city_plate_code) s"
			+ " where exists (SELECT 1"
			+ "          from institution_channel ch, institution_debt_type dt"
			+ "         where ch.channel_code = :channelCode"
			+ "           and ch.is_active = 1"
			+ "           and ch.institution_debt_type_id = dt.id"
			+ "           and dt.institution_id = s.instId"
			+ "           and dt.is_active = 1)"
			+ " group by s.productCode, s.productName, s.cityRelationRequired";		



@Query(value =CITY_PRODUCT_QUERY, nativeQuery=true)
	List<Object[]> getcityProduct(@Param("channelCode") String channelCode, @Param("productFamilyCode") String productFamilyCode);
