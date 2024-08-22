m dual
Hibernate: insert into return_map (create_date, created_by, update_date, updated_by, version, bank_return_code, bank_return_text, institution_return_code, institution_return_text, is_reversible, return_map_code, return_map_definition_id, return_type, id) values (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
Hibernate: select hibernate_sequence.nextval from dual
Hibernate: insert into revinfo (revtstmp, rev) values (?, ?)
Hibernate: insert into return_map_aud (revtype, create_date, created_by, update_date, updated_by, bank_return_code, bank_return_text, institution_return_code, institution_return_text, is_reversible, return_map_code, return_type, return_map_definition_id, id, rev) values (?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
2024-08-22 13:54:57,521 WARN [http-nio-8080-exec-10][SqlExceptionHelper] SQL Error: 904, SQLState: 42000
2024-08-22 13:54:57,521 ERROR [http-nio-8080-exec-10][SqlExceptionHelper] ORA-00904: "RETURN_MAP_DEFINITION_ID": geçersiz belirleyici
