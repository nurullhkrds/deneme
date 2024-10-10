<Form.Item label={ReturnMapFormLocale.reversible.label}>
  <Select
    value={isReversible === true ? "true" : isReversible === false ? "false" : undefined} // Boolean'ı stringe dönüştürme
    onChange={(value) => handleSelectReversible(value === "true")} // "true" stringini boolean true'ya, "false" stringini boolean false'a dönüştürme
    placeholder="Seçiniz"
  >
    <Select.Option value="true">Evet</Select.Option> {/* Boolean true */}
    <Select.Option value="false">Hayır</Select.Option> {/* Boolean false */}
  </Select>
</Form.Item>
