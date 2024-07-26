 useEffect(() => {
    if (formRef.current) {
      const {
        returnMapCode,
        institutionReturnCode,
        institutionReturnText,
        bankReturnCode,
        bankReturnText,
        isReversible,
        returnType,
      } = returnMapOneData || {};

      formRef.current.setFieldsValue({
        returnMapCode: returnMapCode || '',
        institutionReturnCode: institutionReturnCode || '',
        institutionReturnText: institutionReturnText || '',
        bankReturnCode: bankReturnCode || '',
        bankReturnText: bankReturnText || '',
        isReversible: isReversible || false,
        returnType: returnType || '',
      });

      setReturnMapCode(returnMapCode || '');
      setInstitutionReturnCode(institutionReturnCode || '');
      setInstitutionReturnText(institutionReturnText || '');
      setBankReturnCode(bankReturnCode || '');
      setBankReturnText(bankReturnText || '');
      setIsReversible(isReversible || false);
      setReturnType(returnType || '');
    }
  }, [returnMapOneData]);
