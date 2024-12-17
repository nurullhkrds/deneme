<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" ">
   <soapenv:Header/>
   <soapenv:Body>
      <fac:getPaymentLogsByParameters>
         <!--Optional:-->
         <arg0>
            <!--Optional:-->

            <endDate>2024-02-10</endDate>
            <!--Optional:-->
            <institution>GÜLLÜK</institution>
            <!--Optional:-->
            
            <!--Zero or more repetitions:-->
                  <paymentTypeList>
                        <string>I</string>
                        <string>V</string>
                     </paymentTypeList>

           

            <!--Optional:-->
            <product>SU</product>
            <!--Optional:-->
            <startDate>2024-02-01</startDate>
           

            
         </arg0>
      </fac:getPaymentLogsByParameters>
   </soapenv:Body>
</soapenv:Envelope>


<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
   <soap:Header>
      <si xmlns="http://tempuri.org"><![CDATA[LOCALE=,USERID=,PROFILE_TYPE_LIST=,IP=,BRANCHID=,CLIENTHOSTNAME=,CHANNEL=,SESSIONID=,CONVERSATIONID=,CID=,PID=,PROCESSID=,SESSIONDATA=,USERINFO=<userContext><branch></branch><cbsUserCode></cbsUserCode><cprList></cprList><department></department><emailAdd></emailAdd><userCode></userCode><profileList></profileList><userNameSurname></userNameSurname></userContext>,RPID=,TICKETNO=,STATUS=,NQUEUE=,NCID=,SRCTYP=,SRCDT=,TIME=,BE_INVPRC=,BE_EXPL=,BE_ITR=,BE_PRTY=,BE_NUMOFSUBPRC=,BE_AMT=,BE_FILENO=,BE_CCNO=,BE_MRCNO=,BE_NUMOFDC=,BE_CSHTYPE=,BE_FRMCUSID=,BE_FRMCUSSBU=,BE_FRMCUSACC=,BE_FRMCUSACCTYP=,BE_TOCUSID=,BE_TOCUSACC=,BE_TOCUSACCTYP=,BE_INSTDATE=,BE_FTRINSTDATE=,BE_CBSREFNO=,SUBPROCESSID=,ORIGIN=,UPID=,IS_MANUAL_REVERSAL_REQUIRED=,CLIENT_TRANSACTION_ID=,ACTIONCORRELATIONID=,PROCESSCORRELATIONID=,CHNCORRELATIONID=,BROWSER_LANGUAGE=,CHANNELINFO=[CHANNEL=;CUSTOMER_INTERACTION_TYPE=;CUSTOMER_INTERACTION_ID=;CREQUESTID=;CSESSIONID=;CUSERCODE=;],RESTRICT_SP=,LANGUAGE=,APPLICATION_FILTER=,LOG_SP_CALLS=,GORDION_LRM_RISK_MODULE=,AGENT_ID=,]]></si>
   </soap:Header>
   <soap:Body>
      <soap:Fault>
         <faultcode>soap:Client</faultcode>
         <faultstring>Unmarshalling Error: unexpected element (uri:"", local:"string"). Expected elements are (none)</faultstring>
      </soap:Fault>
   </soap:Body>
</soap:Envelope>
