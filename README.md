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

              <Form.Item label="Dönüş Kodu Seç">
                <Select onChange={handleChangeSelectDefinitionId}>
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
