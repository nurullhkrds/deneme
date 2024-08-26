<Form.Item label="ReturnMap Seç">
  <Select
    value={definitionId}
    onChange={handleChangeSelectDefinition}
    virtual={false}  // Sanal listelemeyi devre dışı bırakır
    dropdownMatchSelectWidth={false}  // Açılır menünün genişliğini devre dışı bırakır
  >
    {definitionList.map(item => (
      <Option key={item.id} value={item.id}>
        {item.returnMapCode}
      </Option>
    ))}
  </Select>
</Form.Item>

<Form.Item label="Geri Dönüş Tipi">
  <Select
    value={returnType}
    onChange={handleSelectReturnType}
    virtual={false}
    dropdownMatchSelectWidth={false}
  >
    <Option value="SUCCESS">Başarılı</Option>
    <Option value="ERROR">Başarısız</Option>
  </Select>
</Form.Item>



<Form.Item label="ReturnMap Seç">
  <Select
    value={definitionId}
    onChange={handleChangeSelectDefinition}
    dropdownRender={menu => (
      <div>
        {menu}
      </div>
    )}
  >
    {definitionList.map(item => (
      <Option key={item.id} value={item.id}>
        {item.returnMapCode}
      </Option>
    ))}
  </Select>
</Form.Item>

<Form.Item label="Geri Dönüş Tipi">
  <Select
    value={returnType}
    onChange={handleSelectReturnType}
    dropdownRender={menu => (
      <div>
        {menu}
      </div>
    )}
  >
    <Option value="SUCCESS">Başarılı</Option>
    <Option value="ERROR">Başarısız</Option>
  </Select>
</Form.Item>
