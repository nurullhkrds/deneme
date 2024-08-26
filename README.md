useEffect(() => {
  if (formRef.current) {
    const normalizedData = normalizeReturnMapOneData(returnMapOneData);
    formRef.current.setFieldsValue(normalizedData.formValues);
    setFormState(normalizedData.stateValues);
  }
}, [returnMapOneData]);

const normalizeReturnMapOneData = (data) => {
  const {
    returnMapCode = '',
    institutionReturnCode = '',
    institutionReturnText = '',
    bankReturnCode = '',
    bankReturnText = '',
    isReversible = false,
    returnType = '',
    returnMapDefinition = {}
  } = data || {};

  return {
    formValues: {
      returnMapCode,
      institutionReturnCode,
      institutionReturnText,
      bankReturnCode,
      bankReturnText,
      isReversible,
      returnType,
      definitionId: returnMapDefinition.id || ''
    },
    stateValues: {
      returnMapCode,
      institutionReturnCode,
      institutionReturnText,
      bankReturnCode,
      bankReturnText,
      isReversible,
      returnType,
      definitionId: returnMapDefinition.id
    }
  };
};

const setFormState = (stateValues) => {
  const {
    returnMapCode,
    institutionReturnCode,
    institutionReturnText,
    bankReturnCode,
    bankReturnText,
    isReversible,
    returnType,
    definitionId
  } = stateValues;

  setReturnMapCode(returnMapCode);
  setInstitutionReturnCode(institutionReturnCode);
  setInstitutionReturnText(institutionReturnText);
  setBankReturnCode(bankReturnCode);
  setBankReturnText(bankReturnText);
  setIsReversible(isReversible);
  setReturnType(returnType);
  setDefinitionId(definitionId);
};
