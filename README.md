   <Fragment>
      <Form ref={ref} >
        <Form.Item label="ReturnMap Kodu">
          <input
            name="returnMapCode"
            value={returnMapCode}
            onChange={handleInputChange}
          />
        </Form.Item>
        <Form.Item label="Banka Kodu">
          <input
            name="bankReturnCode"
            value={bankReturnCode}
            onChange={handleInputChange}
          />
        </Form.Item>
        <Form.Item label="Kurum Kodu">
          <input
            name="institutionReturnCode"
            value={institutionReturnCode}
            onChange={handleInputChange}
          />
        </Form.Item>

      </Form>
      <Button onClick={handleSearch}>Search</Button>
      <Button
        type="secondary"
        onClick={handleReset}
      >
        Reset
      </Button>
    </Fragment>
  );
};

export default ReturnMapServiceParametersSearch;
