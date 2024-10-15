<Form>
  {/* returnMap alanı tek başına en başta */}
  <Form.Item
    label={ReturnMapFormLocale.returnMap.label}
    rules={[{ required: true, message: 'Lütfen bir dönüş kodu seçin!' }]}
    colSpan={{ xs: 24, sm: 24, md: 12, lg: 12 }}
  >
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

  {/* institutionReturnCode ve institutionReturnText yan yana */}
  <Row gutter={16}>
    <Col xs={24} sm={12} md={12} lg={12}>
      <Form.Item
        label={ReturnMapFormLocale.institutionReturnCode.label}
        rules={[{ required: true, message: 'Lütfen bir dönüş kodu girin!' }]}
      >
        <input
          validation={[{ required: true }]}
          name="institutionReturnCode"
          value={institutionReturnCode}
          onChange={handleInstitutionReturnCode}
          style={{
            border: "1px solid #dcdcdc",
            borderRadius: "4px",
            padding: "8px",
            fontSize: "14px",
            width: "100%",
          }}
        />
      </Form.Item>
    </Col>
    <Col xs={24} sm={12} md={12} lg={12}>
      <Form.Item
        label={ReturnMapFormLocale.institutionReturnText.label}
        rules={[{ required: true, message: 'Lütfen bir dönüş açıklaması girin!' }]}
      >
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
    </Col>
  </Row>

  {/* bankReturnCode ve bankReturnText yan yana */}
  <Row gutter={16}>
    <Col xs={24} sm={12} md={12} lg={12}>
      <Form.Item
        label={ReturnMapFormLocale.bankReturnCode.label}
        rules={[{ required: true, message: 'Lütfen bir banka hata kodu seçin!' }]}
      >
        <Select
          value={bankReturnCode}
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
    </Col>
    <Col xs={24} sm={12} md={12} lg={12}>
      <Form.Item
        label={ReturnMapFormLocale.bankReturnText.label}
        rules={[{ required: true, message: 'Lütfen bir banka hata açıklaması seçin!' }]}
      >
        <Select
          value={bankReturnText}
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
    </Col>
  </Row>

  {/* Diğer form alanları */}
  <Form.Item label={ReturnMapFormLocale.returnType.label} colSpan={{ xs: 24, sm: 24, md: 12, lg: 12 }}>
    <Select
      showSearch
      allowClear
      defaultValue=""
      onChange={handleSelectReturnType}
      validation={[{ required: true }]}
      value={returnType}
    >
      <Option value="SUCCESS">{ReturnMapFormLocale.returnType.options.success}</Option>
      <Option value="ERROR">{ReturnMapFormLocale.returnType.options.error}</Option>
    </Select>
  </Form.Item>

  <Form.Item label={ReturnMapFormLocale.reversible.label} colSpan={{ xs: 24, sm: 24, md: 12, lg: 12 }}>
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
