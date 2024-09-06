DECLARE
  
  TYPE type_all_subscriber IS RECORD(

    institution   varchar2(10),
    subscriber_no varchar2(30),
    product       varchar2(10)  
    );

  TYPE table_all_subscriber IS TABLE OF type_all_subscriber;
  tAllSubscriber table_all_subscriber;

  v_institution  t_oto_abone.kurum%TYPE;
  v_subscriberNo t_oto_abone.aboneno%TYPE;
  v_product      t_oto_abone.urun%TYPE;
  
  BEGIN
    
  SELECT kurum, aboneno, urun
    BULK COLLECT
    INTO tAllSubscriber
    from (select kurum, aboneno, urun
            from otolive.t_oto_abone s
            where musterino=3693
            and statu ='A'
            and sablontipi = 'L'
			and urun not in ('HAVALE','SGK','DBS','SİGORTA')
			and (s.aboneno,s.urun,s.kurum) IN
			(select a.aboneno,a.urun,a.kurum from otolive.sablon a where  cifno = 3693
			and hesapno ='00169935')
);
            
  FOR i in 1 .. tAllSubscriber.COUNT() LOOP

    variable_audit.p_audit_var('FIX', 'FIX', NULL);

    delete from otolive.sablon
     where aboneno = tAllSubscriber(i).subscriber_no
       and urun = tAllSubscriber(i).product
       and kurum = tAllSubscriber(i).institution
       and cifno = 3623293
       and hesapno ='00169494935';

    update otolive.t_oto_abone
       set sablontipi  = 'K',
           KARTNO      = '156156156156156156156',
           sonkullanma = '0729'
     where aboneno = tAllSubscriber(i).subscriber_no
       and urun = tAllSubscriber(i).product
       and kurum = tAllSubscriber(i).institution
       and musterino=3623293
       and statu ='A'
       and sablontipi = 'L'
	   and urun not in ('HAVALE','SGK','DBS','SİGORTA');

     commit;
  END LOOP;   
END;
/
