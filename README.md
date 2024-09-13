alter table BILL.RETURN_MAP
drop unique (INSTITUTION_RETURN_CODE) cascade;
alter table BILL.RETURN_MAP
add constraint UK_RETURN_MAP_01 unique (RETURN_MAP_DEFINITION_ID, INSTITUTION_RETURN_CODE); 



 declare
  cursor cur_map is 
    select  distinct(RETURN_MAP_CODE) rmc from BILL.RETURN_MAP ;
    
 vseqNo number;
begin
     for rec_map in cur_map loop  
        select BILL.SEQ_RETURN_MAP_DEFINITION.nextval into vseqNo from dual;

      insert into BILL.RETURN_MAP_DEFINITION (ID, VERSION, RETURN_MAP_CODE, IS_ACTIVE, CREATE_DATE, CREATED_BY, UPDATE_DATE, UPDATED_BY)
      values (vseqNo, 0,rec_map.rmc , '1', sysdate, 'U068014', null, null);
      
      update  BILL.RETURN_MAP set RETURN_MAP_DEFINITION_ID = vseqNo where RETURN_MAP_CODE = rec_map.rmc ;
 
    END LOOP;
 
   commit;  
end;
/
