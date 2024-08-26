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
