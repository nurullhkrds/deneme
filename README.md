 useEffect(() => {
Refactor this function to reduce its Cognitive Complexity from 16 to the 15 allowed.
    if (formRef.current) {
      const {
        returnMapCode,
        institutionReturnCode,
        institutionReturnText,
        bankReturnCode,
        bankReturnText,
        isReversible,
        returnType,
        returnMapDefinition
      } = returnMapOneData || {};
      formRef.current.setFieldsValue({
        institutionReturnCode: institutionReturnCode || '',
        institutionReturnText: institutionReturnText || '',
        bankReturnCode: bankReturnCode || '',
        bankReturnText: bankReturnText || '',
        isReversible: isReversible || false,
        returnType: returnType || '',
        definitionId: definitionId || '',
      });
      setReturnMapCode(returnMapCode || '');
      setInstitutionReturnCode(institutionReturnCode || '');
      setInstitutionReturnText(institutionReturnText || '');
      setBankReturnCode(bankReturnCode || '');
      setBankReturnText(bankReturnText || '');
      setIsReversible(isReversible || false);
      setReturnType(returnType || '');
      setDefinitionId(returnMapDefinition?.id)
    }
