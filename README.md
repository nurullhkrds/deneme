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
        returnMapDefinition
      } = returnMapOneData || {};
      formRef.current.setFieldsValue({
        returnMapCode: returnMapCode || '',
        institutionReturnCode: institutionReturnCode || '',
        institutionReturnText: institutionReturnText || '',
        bankReturnCode: bankReturnCode || '',
        bankReturnText: bankReturnText || '',
        isReversible: isReversible || false,
        returnType: returnType || '',
        definitionId: returnMapDefinition.id || '',
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


Refactor this function to reduce its Cognitive Complexity from 17 to the 15 allowed.
