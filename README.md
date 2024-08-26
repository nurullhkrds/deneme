<div>
  <label for="returnTypeSelect">Geri Dönüş Tipi</label>
  <select 
    id="returnTypeSelect"
    name="returnType" 
    value={returnType} 
    onChange={handleSelectReturnType} 
    style={{ width: '100%', padding: '8px', borderRadius: '4px', border: '1px solid #dcdcdc' }}
  >
    <option value="">Seç</option>
    <option value="SUCCESS">Başarılı</option>
    <option value="ERROR">Başarısız</option>
  </select>
</div>
