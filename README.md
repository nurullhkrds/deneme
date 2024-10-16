@Query(value = "SELECT icp.id, " +
        "icp.institution_channel_id, " +
        "icp.institution_process_id, " +
        "icp.working_start_time, " +
        "icp.working_finish_time, " +
        "icp.is_active, " +
        "i.id AS institution_id, " +
        "i.name AS institution_name, " +
        "icp.created_by, " +
        "icp.create_date, " +
        "icp.updated_by, " +
        "icp.update_date " +  // Boşluk eklenmeli
        "FROM institution_channel_process icp " +  // Boşluk eklenmeli
        "JOIN institution_process ip ON icp.institution_process_id = ip.id " +  // Boşluk eklenmeli
        "JOIN institution i ON ip.institution_id = i.id",  // Boşluk eklenmeli
        nativeQuery = true)
List<Object[]> findInstitutionChnlProcessWithInstitution();
