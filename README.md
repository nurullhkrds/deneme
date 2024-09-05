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

              <Form.Item label="Dönüş Kodu Seç"
                rules={[{ required: true, message: 'Lütfen bir dönüş kodu seçin!' }]}

              >

                <Select onChange={handleChangeSelectDefinitionId}>
                  {definitionListIsActiveTrue?.map(item => (
                    <Option key={item.id} value={item.id}>
                      {item.returnMapCode}
                    </Option>
                  ))}
                </Select>
              </Form.Item>

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



  const handleOkForCreate = () => {
    setModalVisible(false);
    if (!definitionId) {
      Notification.error('Lütfen bir dönüş kodu seçin!');
      return;
    }
    sendcreateReturnMapRequest(callApi, createRequest)
      .then(() => {
        Notification.success('Ekleme Başarılı', 3);
      })
      .catch(error => {
        console.error('Error creating return map:', error);
        Notification.error('Hatalı Ekleme', 3);
      });
    setbankReturnCode("")
    setIsReversible(false)
    setReturnMapCode("")
    setbankReturnText("")
    setinstitutionReturnCode("")
    setinstitutionReturnText("")
    setreturnType("")

  };
