  <Form.Item label={ReturnMapFormLocale.returnType.label}>
                <Select showSearch allowClear defaultValue="" onChange={handleSelectReturnType} validation={[{ required: true }]}
                >
                  <Option value="SUCCESS">{ReturnMapFormLocale.returnType.options.success}</Option>
                  <Option value="ERROR">{ReturnMapFormLocale.returnType.options.error}</Option>
                </Select>
              </Form.Item>
