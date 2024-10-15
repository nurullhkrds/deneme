 <Form>
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

              <Form.Item label={ReturnMapFormLocale.institutionReturnCode.label}>
                <input
                  validation={[{ required: true }]}
                  name="institutionReturnCode"
                  value={institutionReturnCode}
                  onChange={(e) => {
                    handleInstitutionReturnCode(e);

                  }}
                  style={{
                    border: "1px solid #dcdcdc",
                    borderRadius: "4px",
                    padding: "8px",
                    fontSize: "14px",
                    width: "100%",
                  }}
                />
              </Form.Item>

              <Form.Item label={ReturnMapFormLocale.institutionReturnText.label}>
                <input
                  validation={[{ required: true }]}
                  name="institutionReturnText"
                  value={institutionReturnText}
                  onChange={handleInstitutionReturnText}
                  style={{
                    border: "1px solid #dcdcdc",
                    borderRadius: "4px",
                    padding: "8px",
                    fontSize: "14px",
                    width: "100%",
                  }}
                />
              </Form.Item>
              <Form.Item
                label={ReturnMapFormLocale.bankReturnCode.label}
                rules={[{ required: true, message: 'Lütfen bir banka hata kodu seçiniz !' }]}
              >
                <Select
                  value={bankReturnCode}
                  validation={[{ required: true }]}
                  onChange={handleBankReturnCodeChange}
                  showSearch
                  placeholder="Seçiniz"
                >
                  {bankReturnData.map((item) => (
                    <Option key={item.code} value={item.code}>
                      {item.code}
                    </Option>
                  ))}
                </Select>
              </Form.Item>

              <Form.Item
                label={ReturnMapFormLocale.bankRetubankReturnTextrnCode.label}
                rules={[{ required: true, message: 'Lütfen bir banka hata açıklaması seçiniz!' }]}
              >
                <Select
                  value={bankReturnText}
                  validation={[{ required: true }]}
                  onChange={handleBankReturnTextChange}
                  showSearch
                  placeholder="Seçiniz"
                >
                  {bankReturnData.map((item) => (
                    <Option key={item.text} value={item.text}>
                      {item.text}
                    </Option>
                  ))}
                </Select>
              </Form.Item>

              <Form.Item label={ReturnMapFormLocale.returnType.label}>
                <Select
                  showSearch
                  allowClear
                  defaultValue=""
                  onChange={handleSelectReturnType} validation={[{ required: true }]}
                  value={returnType}
                >
                  <Option value="SUCCESS">{ReturnMapFormLocale.returnType.options.success}</Option>
                  <Option value="ERROR">{ReturnMapFormLocale.returnType.options.error}</Option>
                </Select>
              </Form.Item>

              <Form.Item label={ReturnMapFormLocale.reversible.label}>
                <Select
                  value={isReversible}
                  defaultValue={undefined}
                  onChange={handleSelectReversible}
                  validation={[{ required: true }]}
                >
                  <Option value="true">Evet</Option>
                  <Option value="false">Hayır</Option>
                </Select>
              </Form.Item>
            </Form>
