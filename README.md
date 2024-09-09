   const numericPattern = /^[0-9]+$/;
    if (!numericPattern.test(institutionReturnCode)) {
      Notification.error('Kurum Kodu sadece rakamlardan oluşmalıdır!');
      return;
    }
    if (!numericPattern.test(bankReturnCode)) {
      Notification.error('Banka Kodu sadece rakamlardan oluşmalıdır!');
      return;
    }


  const handleBankReturnCode = (e) => {
    setbankReturnCode(e.target.value)
  }

  const handleInstitutionReturnCode = (e) => {
    setinstitutionReturnCode(e.target.value)
  }
