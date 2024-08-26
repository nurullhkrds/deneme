     <Form.Item label="ReturnMap Seç">
                <Select value={definitionId} onChange={handleChangeSelectDefinition}>
                  {definitionList.map(item => (
                    <Option key={item.id} value={item.id}>
                      {item.returnMapCode}
                    </Option>
                  ))}
                </Select>
              </Form.Item>



 <Form.Item label="Geri Dönüş Tipi">
                <Select defaultValue={returnType}

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



ERROR
ResizeObserver loop completed with undelivered notifications.
