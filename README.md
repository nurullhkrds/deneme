CREATE OR REPLACE TRIGGER CBSLIVE.TR_SABLON
BEFORE INSERT OR UPDATE OR DELETE ON SABLON
REFERENCING NEW AS NEW OLD AS OLD
FOR EACH ROW
DECLARE
  v_oper         varchar2(1);

  v_old_cifno    SABLON.CIFNO%TYPE;
  v_old_urun     SABLON.URUN%TYPE;
  v_old_kurum    SABLON.KURUM%TYPE;
  v_old_aboneno  SABLON.ABONENO%TYPE;
  v_old_sirano   SABLON.SIRANO%TYPE;
  v_old_hesapno  SABLON.HESAPNO%TYPE;
  v_old_doviz    SABLON.DOVIZ%TYPE;

  v_new_cifno    SABLON.CIFNO%TYPE;
  v_new_urun     SABLON.URUN%TYPE;
  v_new_kurum    SABLON.KURUM%TYPE;
  v_new_aboneno  SABLON.ABONENO%TYPE;
  v_new_sirano   SABLON.SIRANO%TYPE;
  v_new_hesapno  SABLON.HESAPNO%TYPE;
  v_new_doviz    SABLON.DOVIZ%TYPE;
BEGIN
  IF inserting THEN
     v_oper := 'A';
     if :NEW.OID IS NULL then
        SELECT S_SABLON_OID.NEXTVAL INTO :NEW.OID FROM DUAL;
     end if;
  ELSIF deleting THEN
     v_oper := 'D';
  ELSE
     v_oper := 'C';
  END IF;

  IF inserting OR updating THEN
     v_new_cifno   := :NEW.CIFNO;
     v_new_urun    := :NEW.URUN;
     v_new_kurum   := :NEW.KURUM;
     v_new_aboneno := :NEW.ABONENO;
     v_new_sirano  := :NEW.SIRANO;
     v_new_hesapno := :NEW.HESAPNO;
     v_new_doviz   := :NEW.DOVIZ;
  END IF;

  IF deleting OR updating THEN
     v_old_cifno   := :OLD.CIFNO;
     v_old_urun    := :OLD.URUN;
     v_old_kurum   := :OLD.KURUM;
     v_old_aboneno := :OLD.ABONENO;
     v_old_sirano  := :OLD.SIRANO;
     v_old_hesapno := :OLD.HESAPNO;
     v_old_doviz   := :OLD.DOVIZ;
  END IF;

  INSERT INTO SABLON_AUDIT (KULLANICI, KULLANICI_ONAY, EKRAN, ISLEM, SAAT, TARIH,
  OLD_CIFNO, OLD_URUN, OLD_KURUM, OLD_ABONENO, OLD_SIRANO, OLD_HESAPNO, NEW_DOVIZ,
  NEW_CIFNO, NEW_URUN, NEW_KURUM, NEW_ABONENO, NEW_SIRANO, NEW_HESAPNO, OLD_DOVIZ)
  VALUES(otolive.Variable_Audit.aud_kullanici, otolive.Variable_Audit.aud_kullanici_onay,  otolive.Variable_Audit.aud_ekran, v_oper, TO_CHAR(SYSDATE,'HH24:MI'), TRUNC(SYSDATE),
  v_old_cifno, v_old_urun, v_old_kurum, v_old_aboneno, v_old_sirano, v_old_hesapno, v_new_doviz,
  v_new_cifno, v_new_urun, v_new_kurum, v_new_aboneno, v_new_sirano, v_new_hesapno, v_old_doviz);
END;
