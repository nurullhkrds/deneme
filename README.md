      <select
              id="returnType"
              name="returnType"
              value={returnType}
              onChange={(e) => handleSelectReturnType(e.target.value)}
              style={{ width: '100%', padding: '8px', borderRadius: '4px', border: '1px solid #dcdcdc' }}
            >
              <option value="" selected={returnType === ""}>Seç</option>
              <option value="SUCCESS" selected={returnType === "SUCCESS"}>Başarılı</option>
              <option value="ERROR" selected={returnType === "ERROR"}>Başarısız</option>
            </select>
