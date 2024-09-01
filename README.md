useEffect(() => {
    if (formRef.current && returnMapOneData) {
      const {
        returnMapCode = '',
        institutionReturnCode = '',
        institutionReturnText = '',
        bankReturnCode = '',
        bankReturnText = '',
        isReversible = false,
        returnType = '',
        returnMapDefinition = {}
      } = returnMapOneData;

      const newFields = {
        institutionReturnCode,
        institutionReturnText,
        bankReturnCode,
        bankReturnText,
        isReversible,
        returnType,
        definitionId: returnMapDefinition?.id || ''
      };

      formRef.current.setFieldsValue(newFields);

      setReturnMapCode(returnMapCode);
      setInstitutionReturnCode(institutionReturnCode);
      setInstitutionReturnText(institutionReturnText);
      setBankReturnCode(bankReturnCode);
      setBankReturnText(bankReturnText);
      setIsReversible(isReversible);
      setReturnType(returnType);
      setDefinitionId(returnMapDefinition?.id || '');
    }
}, [returnMapOneData]);
