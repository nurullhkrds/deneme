alter table BILL.RETURN_MAP
add constraint FK_RETURN_MAP_01 foreign key (RETURN_MAP_DEFINITION_ID)
references BILL.RETURN_MAP_DEFINITION (ID);
/
