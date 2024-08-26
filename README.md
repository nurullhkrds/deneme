   <Modal
          title="ReturnMap"
          visible={modalVisible}
          onClose={handleCancelForUpdate}
          footer={[
            <SecureButton permission="handleCancelForUpdate" key="cancel" onClick={handleCancelForUpdate}>
              İptal
            </SecureButton>,
            <SecureButton permission="handleOkForUpdate" key="ok" type="primary" onClick={handleOkForUpdate}>
              Kaydet
            </SecureButton>
          ]}
        >
          <div>
            <Form ref={formRef}>
              <Form.Item label="ReturnMap Seç">
                <Select value={definitionId} onChange={handleChangeSelectDefinition}
                  allowClear={true}
                >
                  {definitionList.map(item => (
                    <Option key={item.id} value={item.id}>
                      {item.returnMapCode}
                    </Option>
                  ))}
                </Select>
              </Form.Item>


              <Form.Item label="Kurum Kodu">
                <input
                  name="institutionReturnCode"
                  value={institutionReturnCode}
                  onChange={handleInstitutionReturnCode}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
                />
              </Form.Item>
              <Form.Item label="Kurum Metni">
                <input
                  name="institutionReturnText"
                  value={institutionReturnText}
                  onChange={handleInstitutionReturnText}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
                />
              </Form.Item>
              <Form.Item label="Banka Kodu">
                <input
                  name="bankReturnCode"
                  value={bankReturnCode}
                  onChange={handleBankReturnCode}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
                />
              </Form.Item>
              <Form.Item label="Banka Metni">
                <input
                  name="bankReturnText"
                  value={bankReturnText}
                  onChange={handleBankReturnText}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
                />
              </Form.Item>

              <Form.Item label="Geri Dönüş Tipi">
                <Select
                  allowClear={true}
                  value={returnType}
                  name="returnType"
                  onChange={handleSelectReturnType}>
                  <Option value="SUCCESS">
                    Başarılı
                  </Option>
                  <Option value="ERROR" >
                    Başarısız
                  </Option>
                </Select>
              </Form.Item>
              <Form.Item >
                <Checkbox textLabel="Geri Dönüşüm" checked={isReversible} onChange={handleSelectReversible} />
              </Form.Item>
            </Form>
          </div>
        </Modal>



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



  const dataWithKeys = useMemo(() =>
    returnMapData.map((item, index) => ({
      ...item,
      key: index + 1,
      returnMapCode: item.returnMapDefinition?.returnMapCode || item.returnMapCode
    })),
    [returnMapData]
  );

ResizeObserver loop completed with undelivered notifications.
    at handleError (http://localhost:3000/Harmoni/sparx/bill-ui-payments/static/js/bundle.js:248880:58)
    at http://localhost:3000/Harmoni/sparx/bill-ui-payments/static/js/bundle.js:248899:7



ReturnMapServiceParametersTable.js:85  Warning: You cannot set a form field before rendering a field associated with the value.
