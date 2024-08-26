<Form.Item label="Geri Dönüş Tipi">
  <select
    id="returnType"
    name="returnType"
    value={returnType}
    onChange={(e) => handleSelectReturnType(e.target.value)} // value'nun değişikliğini handle ediyoruz
    style={{ width: '100%', padding: '8px', borderRadius: '4px', border: '1px solid #dcdcdc' }}
  >
    <option value="">Seç</option>
    <option value="SUCCESS">Başarılı</option>
    <option value="ERROR">Başarısız</option>
  </select>
</Form.Item>
