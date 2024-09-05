const handleOkForCreate = () => {
  if (!definitionId) {
    Notification.error('Lütfen bir dönüş kodu seçin!');
    return;
  }
  
  // Check if any of the required fields are empty
  if (!institutionReturnCode || !bankReturnCode || !institutionReturnText || !bankReturnText || !returnType) {
    Notification.error('Lütfen tüm alanları doldurun!');
    return;
  }
  
  // Validate numeric values for institutionReturnCode and bankReturnCode
  const numericPattern = /^[0-9]+$/;
  if (!numericPattern.test(institutionReturnCode)) {
    Notification.error('Kurum Kodu sadece sayılardan oluşmalıdır!');
    return;
  }
  if (!numericPattern.test(bankReturnCode)) {
    Notification.error('Banka Kodu sadece sayılardan oluşmalıdır!');
    return;
  }
  
  // If all validations pass, proceed with the create request
  setModalVisible(false);
  
  sendcreateReturnMapRequest(callApi, createRequest)
    .then(() => {
      Notification.success('Ekleme Başarılı', 3);
    })
    .catch(error => {
      console.error('Error creating return map:', error);
      Notification.error('Hatalı Ekleme', 3);
    });
  
  // Reset form fields after submission
  setbankReturnCode("");
  setIsReversible(false);
  setReturnMapCode("");
  setbankReturnText("");
  setinstitutionReturnCode("");
  setinstitutionReturnText("");
  setreturnType("");
};
