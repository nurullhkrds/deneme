<div>
  <label for="returnTypeSelect">Geri Dönüş Tipi</label>
  <select 
    id="returnTypeSelect"
    name="returnType" 
    value={returnType} 
    onChange={handleSelectReturnType} 
    style={{ width: '100%', padding: '8px', borderRadius: '4px', border: '1px solid #dcdcdc' }}
  >
    <option value="" selected={returnType === ""}>Seç</option>
    <option value="SUCCESS" selected={returnType === "SUCCESS"}>Başarılı</option>
    <option value="ERROR" selected={returnType === "ERROR"}>Başarısız</option>
  </select>
</div>
