  <Form.Item
          label={InstServiceParameterDetailModalFormLocale.status.label}
          colSpan={{ xs: 24, sm: 24, md: 12, lg: 12 }}
        >
          <Select
            name="isActive"
            allowClear
            validation={[{ required: true }]}
            data={[
              {
                value: "true",
                label:
                  InstServiceParameterDetailModalFormLocale.status.options.active,
              },
              {
                value: "false",
                label:
                  InstServiceParameterDetailModalFormLocale.status.options
                    .passive,
              },
            ]}
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
