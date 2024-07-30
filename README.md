 <Fragment>
      <Form ref={ref} >
        <Form.Item label="ReturnMap Kodu">
          <input
            name="returnMapCode"
            value={returnMapCode}
            onChange={handleInputChange}
            style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}


          />
        </Form.Item>
        <Form.Item label="Banka Kodu">
          <input
            name="bankReturnCode"
            value={bankReturnCode}
            onChange={handleInputChange}
            style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}

          />
        </Form.Item>
        <Form.Item label="Kurum Kodu">
          <input
            name="institutionReturnCode"
            value={institutionReturnCode}
            onChange={handleInputChange}
            style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}

          />
        </Form.Item>

      </Form>
      <SecureButton permission="handleSearch" onClick={handleSearch}>
        <IconBox name={"search"} />

      </SecureButton>
      <SecureButton
        type="secondary"
        permission="handleReset"
        onClick={handleReset}
      >
        Reset
      </SecureButton>
    </Fragment>
