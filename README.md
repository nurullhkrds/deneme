       <Form.Item label={ReturnMapFormLocale.returnMap.label} rules={[{ required: true, message: 'Lütfen bir dönüş kodu seçin!' }]}>
                <Select
                  value={definitionId}
                  defaultValue={definitionId}
                  onChange={handleChangeSelectDefinitionId}
                  disabled
                >
                  {definitionList?.map((item) => (
                    <Option key={item.id} value={item.id}>
                      {item.returnMapCode}
                    </Option>
                  ))}
                </Select>
              </Form.Item>
