<select
  id="returnType"
  name="returnType"
  value={returnType}
  onChange={(e) => handleSelectReturnType(e.target.value)}
  style={{
    width: '100%',
    padding: '8px',
    borderRadius: '4px',
    border: '1px solid #d9d9d9', // Ant Design'daki gri kenarlık
    boxShadow: '0 2px 8px rgba(0, 0, 0, 0.1)', // Hafif gölge
    transition: 'border-color 0.3s ease', // Geçiş efekti
    backgroundColor: '#fff', // Beyaz arka plan
    fontSize: '14px', // Font boyutu
    outline: 'none' // Focus olduğunda dış çizgi olmasın
  }}
>
  <option value="" selected={returnType === ""}>Seç</option>
  <option value="SUCCESS" selected={returnType === "SUCCESS"}>Başarılı</option>
  <option value="ERROR" selected={returnType === "ERROR"}>Başarısız</option>
</select>
