(
    <div>
      <div style={{ display: 'flex', alignItems: 'center' }}>
        <Form ref={ref} style={{ display: 'flex', flex: 1, alignItems: 'center' }}>
          <Form.Item label="ReturnMap Kodu" style={{ flex: 1, marginRight: '10px' }}>
            <input
              name="returnMapCode"
              value={returnMapCode}
              onChange={handleInputChange}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>
          <Form.Item label="Banka Kodu" style={{ flex: 1, marginRight: '10px' }}>
            <input
              name="bankReturnCode"
              value={bankReturnCode}
              onChange={handleInputChange}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>
          <Form.Item label="Kurum Kodu" style={{ flex: 1, marginRight: '10px' }}>
            <input
              name="institutionReturnCode"
              value={institutionReturnCode}
              onChange={handleInputChange}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>
        </Form>
        <div style={{ display: 'flex', justifyContent: 'flex-end' }}>
          <SecureButton permission="handleSearch" onClick={handleSearch} style={{ marginLeft: '10px' }}>
            <IconBox name={"search"} />
          </SecureButton>
          <SecureButton type="secondary" permission="handleReset" onClick={handleReset} style={{ marginLeft: '10px' }}>
            Reset
          </SecureButton>
        </div>
      </div>
    </div>
  );
