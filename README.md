  <select
    id="definitionId"
    name="definitionId"
    value={definitionId}
    onChange={(e) => handleChangeSelectDefinition(e.target.value)}
    style={{ width: '100%', padding: '8px', borderRadius: '4px', border: '1px solid #dcdcdc' }}
  >
    <option value="" selected={definitionId === ""}>Seç</option>
    {definitionList.map(item => (
      <option key={item.id} value={item.id} selected={definitionId === item.id}>
        {item.returnMapCode}
      </option>
    ))}
  </select>
