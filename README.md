       <Form.Item label="Geri Dönüş Tipi">
                <select
                  id="returnType"
                  name="returnType"
                  value={returnType}
                  onChange={handleSelectReturnType}
                  style={{ width: '100%', padding: '8px', borderRadius: '4px', border: '1px solid #dcdcdc' }}
                >
                  <option value="" selected={returnType === ""}>Seç</option>
                  <option value="SUCCESS" selected={returnType === "SUCCESS"}>Başarılı</option>
                  <option value="ERROR" selected={returnType === "ERROR"}>Başarısız</option>
                </select>
              </Form.Item>


 const handleSelectReturnType = value => {
    setReturnType(value);
  };

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



  }, [returnMapOneData]);
