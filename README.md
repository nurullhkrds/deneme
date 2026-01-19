DECLARE
  v_txt  CLOB := q'[
'SU_EDİRNE_LOAD_DEBTS','SU_EDİRNE_ALL');
]';
  v_i PLS_INTEGER := 1;
  v_name VARCHAR2(4000);
  v_code VARCHAR2(4000);

  FUNCTION next_val(p_txt IN CLOB, p_pos IN OUT PLS_INTEGER) RETURN VARCHAR2 IS
  BEGIN
    p_pos := p_pos + 1;
    RETURN REGEXP_SUBSTR(p_txt, '''([^'']*)''', 1, p_pos-1, 'n', 1);
  END;
BEGIN
  LOOP
    v_name := next_val(v_txt, v_i);
    v_code := next_val(v_txt, v_i);
    EXIT WHEN v_name IS NULL OR v_code IS NULL;

    UPDATE bill.service s
       SET s.return_map_code = v_code
     WHERE INSTR(s.name, v_name) > 0;

    DBMS_OUTPUT.PUT_LINE(v_name || ' -> ' || v_code || ' | updated=' || SQL%ROWCOUNT);
  END LOOP;
END;
/
