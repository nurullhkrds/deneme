<Form.Item label="Geri Dönüş Tipi" colSpan={{ xs: 24, sm: 24, md: 12, lg: 12 }}>
  <Select
    name="returnType"
    allowClear
    validation={[{ required: true }]}
    value={returnType}
    onChange={handleSelectReturnType}
    data={[
      { value: "SUCCESS", label: "Başarılı" },
      { value: "ERROR", label: "Başarısız" },
    ]}
  />
</Form.Item>
