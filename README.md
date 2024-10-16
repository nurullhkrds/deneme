	@Query(value = "SELECT icp.id, " +
			"icp.institution_channel_id, " +
			"icp.institution_process_id, " +
			"icp.working_start_time, " +
			"icp.working_finish_time, " +
			"icp.is_active, " +
			"i.id AS institution_id, " +
			"i.name AS institution_name," +
			"icp.created_by,"+
			"icp.create_date,"+
			"icp.updated_by,"+
			"icp.update_date"+
			"FROM institution_channel_process icp" +
			"JOIN institution_process ip ON icp.institution_process_id = ip.id " +
			"JOIN institution i ON ip.institution_id = i.id",
			nativeQuery = true)
	List<Object[]> findInstitutionChnlProcessWithInstitution();
