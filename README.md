<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:tem="http://tempuri.org/" xmlns:prob="http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts">
   <soapenv:Header/>
   <soapenv:Body>
      	<tem:KentliBorclariniOde>
<!-- Optional: -->
		<tem:borcTahsilati>
		<!-- Optional: -->
		<prob:Aciklama>?</prob:Aciklama>
		<!-- Optional: -->
		<prob:AdSoyad>?</prob:AdSoyad>
		<!-- Optional: -->
		<prob:BorcDetaylari>
				<prob:BorcDetayi>
				<!-- Optional: -->
						<prob:AboneKodu>?</prob:AboneKodu>
						<!-- Optional: -->
						<prob:BeyanAdres>?</prob:BeyanAdres>
						<!-- Optional: -->
						<prob:BeyanAnaId>6799410</prob:BeyanAnaId>
						<!-- Optional: -->
						<prob:BeyanSiraNo>3</prob:BeyanSiraNo>
						<!-- Optional: -->
						<prob:BorcTutari>312.99</prob:BorcTutari>
						<!-- Optional: -->
						<prob:GecikmeTutari>0</prob:GecikmeTutari>
						<!-- Optional: -->
						<prob:HesapAdi>?</prob:HesapAdi>
						<!-- Optional: -->
						<prob:HesapId>741</prob:HesapId>
						<!-- Optional: -->
						<prob:SayacSeriNo>?</prob:SayacSeriNo>
						<!-- Optional: -->
						<prob:SistemAdi>?</prob:SistemAdi>
						<!-- Optional: -->
						<prob:SistemId>16</prob:SistemId>
						<!-- Optional: -->
						<prob:Taksit>2</prob:Taksit>
						<!-- Optional: -->
						<prob:ToplamTutar>312.99</prob:ToplamTutar>
						<!-- Optional: -->
						<prob:VadeTarihi>2024-11-30T00:00:00</prob:VadeTarihi>
						<!-- Optional: -->
						<prob:Yil>2024</prob:Yil>
				</prob:BorcDetayi>
		</prob:BorcDetaylari>
		
		<prob:KentliId>173917</prob:KentliId>
		<prob:ReferansNo>1s204</prob:ReferansNo>
		<prob:KimlikNo>30055934852</prob:KimlikNo>
		<prob:TahsilatTarihi>2024-10-25T15:12:36.220Z</prob:TahsilatTarihi>
		<prob:Tarih>2024-10-25T15:12:36.220Z</prob:Tarih>
		<prob:ToplamTutar>312.99</prob:ToplamTutar>
		</tem:borcTahsilati>
	</tem:KentliBorclariniOde>
   </soapenv:Body>
</soapenv:Envelope>


bu soaptan attığım istek ve başarılı sonuç alabiliyorum. 










<soap:Envelope
xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
>
<soap:Body>
<KentliBorclariniOde
xmlns="http://tempuri.org/"
xmlns:ns2="http://schemas.datacontract.org/2004/07/Probel.WS.EBL.DataContracts"
xmlns:ns3="http://schemas.datacontract.org/2004/07/Probel.Entity.EBL"
xmlns:ns4="http://schemas.microsoft.com/2003/10/Serialization/Arrays"
xmlns:ns5="http://schemas.microsoft.com/2003/10/Serialization/"
xmlns:ns6="http://schemas.datacontract.org/2004/07/Probel.Core"
>
<borcTahsilati>
<AdSoyad
xmlns:ns7="http://tempuri.org/"
>string</AdSoyad>
<BorcDetaylari
xmlns:ns7="http://tempuri.org/"
>
<ns2:BorcDetayi>
<ns2:BeyanAnaId>6799410</ns2:BeyanAnaId>
<ns2:BeyanSiraNo>3</ns2:BeyanSiraNo>
<ns2:BorcTutari>312.99</ns2:BorcTutari>
<ns2:GecikmeTutari>0</ns2:GecikmeTutari>
<ns2:HesapId>741</ns2:HesapId>
<ns2:SistemId>16</ns2:SistemId>
<ns2:Taksit>2</ns2:Taksit>
<ToplamTutar>312.99</ToplamTutar>
<ns2:VadeTarihi>2024-11-30T00:00:00.000+03:00</ns2:VadeTarihi>
<ns2:Yil>2024</ns2:Yil>
</ns2:BorcDetayi>
</BorcDetaylari>
<ns2:KentliId>173917</ns2:KentliId>
<KimlikNo
xmlns:ns7="http://tempuri.org/"
>30055934852</KimlikNo>
<ReferansNo
xmlns:ns7="http://tempuri.org/"
>string</ReferansNo>
<TahsilatTarihi
xmlns:ns7="http://tempuri.org/"
>2024-10-26T00:00:00.000+03:00</TahsilatTarihi>
<ns2:Tarih>2024-10-26T00:00:00.000+03:00</ns2:Tarih>
<ns2:ToplamTutar>312.99</ns2:ToplamTutar>
</borcTahsilati>
</KentliBorclariniOde>
</soap:Body>
</soap:Envelope>

bu ise javadan attığım istek bu şekilde gitmektedir. 

<b:Kod>Basarisiz</b:Kod>
<b:Mesaj>Value cannot be null.-Parameter name: source</b:Mesaj>
