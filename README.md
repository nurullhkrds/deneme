   <section>
        <Modal
          title="ReturnMap"
          visible={modalVisible}
          onClose={handleCancelForCreate}
          footer={[
            <SecureButton permission="handleCancelForCreate" key="cancel" onClick={handleCancelForCreate}>
              Vazgeç
            </SecureButton>,
            <SecureButton permission="handleOkForCreate" key="ok" type="primary" onClick={handleOkForCreate}>
              Kaydet
            </SecureButton>
          ]}
        >

          <div>
            <Form >

              <Form.Item label="Kurum Kodu">
                <input
                  name="institutionReturnCode"
                  placeholder='0202'
                  value={institutionReturnCode}
                  onChange={handleInstitutionReturnCode}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}
                />
              </Form.Item>
              <Form.Item label="Kurum Metni">
                <input
                  name="institutionReturnText"
                  value={institutionReturnText}
                  placeholder='Genel Hata'
                  onChange={handleInstitutionReturnText}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}
                />
              </Form.Item>
              <Form.Item label="Banka Kodu">
                <input
                  placeholder='0303'
                  name="bankReturnCode"
                  value={bankReturnCode}
                  onChange={handleBankReturnCode}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}
                />
              </Form.Item>
              <Form.Item label="Banka Metni">
                <input
                  placeholder='genel hata'
                  name="bankReturnText"
                  value={bankReturnText}
                  onChange={handleBankReturnText}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}
                />
              </Form.Item>

              <Form.Item label="Geri Dönüş Tipi">
                <Select onChange={handleSelectReturnType}>
                  <Option value="SUCCESS">
                    Başarılı
                  </Option>
                  <Option value="ERROR" >
                    Başarısız
                  </Option>
                </Select>
              </Form.Item>
              <Form.Item>

                <Form.Item >
                  <Checkbox textLabel="Geri Dönüşüm" checked={isReversible} onChange={handleSelectReversible} />
                </Form.Item>

              </Form.Item>
            </Form>
          </div>
        </Modal>

      </section>




data=[
  {
      "id": 100007,
      "returnMapCode": "v",
      "isActive": true,
      "institutions": null
    },
    {
      "id": 100008,
      "returnMapCode": "vc",
      "isActive": true,
      "institutions": null
    },
    {
      "id": 100009,
      "returnMapCode": "lolo1",
      "isActive": true,
      "institutions": null
    },
    {
      "id": 100010,
      "returnMapCode": "lolo2",
      "isActive": true,
      "institutions": null
    },
    {
      "id": 100011,
      "returnMapCode": "dada",
      "isActive": true,
      "institutions": null
    }
]
