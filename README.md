Hibernate: select returnmapd0_.id as id1_61_, returnmapd0_.create_date as create_date2_61_, returnmapd0_.created_by as created_by3_61_, returnmapd0_.update_date as update_date4_61_, returnmapd0_.updated_by as updated_by5_61_, returnmapd0_.version as version6_61_, returnmapd0_.is_active as is_active7_61_, returnmapd0_.return_map_code as return_map_code8_61_ from return_map_definition returnmapd0_ where returnmapd0_.return_map_code=?
Hibernate: select seq_return_map_definition.nextval from dual
2024-08-21 18:11:58,695 WARN [http-nio-8080-exec-1][SqlExceptionHelper] SQL Error: 2289, SQLState: 42000
2024-08-21 18:11:58,696 ERROR [http-nio-8080-exec-1][SqlExceptionHelper] ORA-02289: sıra mevcut değil

2024-08-21 18:11:58,722 ERROR [http-nio-8080-exec-1][PymExceptionHandler] An Exception occured org.springframework.dao.InvalidDataAccessResourceUsageException: could not extract ResultSet; SQL [n/a]; nested exception is org.hibernate.exception.SQLGrammarException: could not extract ResultSet
	at org.springframework.orm.jpa.vendor.HibernateJpaDialect.convertHibernateAccessException(HibernateJpaDialect.java:259)
	at org.springframework.orm.jpa.vendor.HibernateJpaDialect.translateExceptionIfPossible(HibernateJpaDialect.java:233)
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.translateExceptionIfPossible(AbstractEntityManagerFactoryBean.java:551)
