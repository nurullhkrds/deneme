return (

    <>
      <Table rowSelection={rowSelection} columns={columns} data={dataWithKeys}
        loading={{ text: 'Veriler listeleniyor...', status: spinnig }}
      />

      <section>
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
                <Select value={definitionId} onChange={handleChangeSelectDefinition}>
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

                  value={returnType}
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

      </section>
    </>
  );
};

export default ReturnMapServiceParametersTable;





  const columns = [
    {
      title: 'Dönüş Kodu',
      dataIndex: 'returnMapCode',
      key: 'returnMapCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Kurum Kodu',
      dataIndex: 'institutionReturnCode',
      key: 'institutionReturnCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Kurum Metni',
      dataIndex: 'institutionReturnText',
      key: 'institutionReturnText',
      width: 200,
      resizable: true,
    },
    {
      title: 'Banka Kodu',
      dataIndex: 'bankReturnCode',
      key: 'bankReturnCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Banka Metni',
      dataIndex: 'bankReturnText',
      key: 'bankReturnText',
      width: 200,
      resizable: true,
    },
    {
      title: 'Dönüş Tipi',
      dataIndex: 'returnType',
      key: 'returnType',
      width: 200,
      resizable: true,
    },
    {
      title: 'Geri Dönüş',
      dataIndex: 'isReversible',
      key: 'isReversible',
      width: 200,
      render: value => (value ? 'Evet' : 'Hayır'),
      resizable: true,
    },
    {
      key: "edit",
      width: 50,
      align: "center",
      render: (text, record) => (
        <SecureButton
          type="primary"
          size="small"
          title={"Güncelle"}
          permission="handleEdit"
          onClick={() => handleEdit(record)}
        >
          <Icon name="edit" size="small" />
        </SecureButton>
      ),
    },
    {
      key: 'actions',
      width: 100,
      render: (text, record) => (
        <DeleteButton
          type="danger"
          size="small"
          title={"Sil"}
          permission="handleClickOneDelete"
          onClick={() => {
            handleClickOneDelete(record);
          }}
        >
          <Icon name="trash" size="small" />
        </DeleteButton>
      )
    }
  ];
  const returnMapOneData = useSelector((state) => state.returnMap.returnMapData);

  const updateData = {
    id: updatedId,
    institutionReturnCode: institutionReturnCode,
    institutionReturnText: institutionReturnText,
    bankReturnCode: bankReturnCode,
    bankReturnText: bankReturnText,
    returnType: returnType,
    isReversible: isReversible,
    returnMapDefinitionId: definitionId
  }
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


  }, [returnMapOneData]);

 {
      "id": 2950001,
      "returnMapDefinition": {
        "id": 100003,
        "returnMapCode": "ekleme4",
        "isActive": true,
        "institutions": null
      },
      "returnMapCode": "ekleme4",
      "institutionReturnCode": "21",
      "institutionReturnText": "kurummetni",
      "bankReturnCode": "213",
      "bankReturnText": "banka",
      "returnType": "SUCCESS",
      "isReversible": true
    }, bu şekil veri dönmektedir. tablodaki "returnMapCode" kısma returnMapDefinition içindeki returnMapCode'yi eklemek istiyorun ..
    
