const ReturnMapServiceParametersSearch = ({ callApi }) => {
  const dispatch = useDispatch();
  const [returnMapCode, setReturnMapCode] = useState('');
  const [bankReturnCode, setBankReturnCode] = useState('');
  const [institutionReturnCode, setInstitutionReturnCode] = useState('');

  const handleInputChange = (e) => {
    const { name, value } = e.target;
    if (name === 'returnMapCode') {
      setReturnMapCode(value);
    } else if (name === 'bankReturnCode') {
      setBankReturnCode(value);
    } else if (name === 'institutionReturnCode') {
      setInstitutionReturnCode(value);
    }
  };

  const handleSearch = () => {
    const searchCriteria = {
      returnMapCode,
      bankReturnCode,
      institutionReturnCode,
    };
    dispatch(fetchInstitutionsData(dispatch, callApi, searchCriteria));
  };

  const handleReset = () => {
    setReturnMapCode('');
    setBankReturnCode('');
    setInstitutionReturnCode('');
    dispatch(fetchInstitutionsData(dispatch, callApi, {}));
  };

  return (
    <Fragment>
      <Form>
        <Form.Item label="ReturnMap Kodu">
          <TextInput
            name="returnMapCode"
            value={returnMapCode}
            onChange={handleInputChange}
            allowClear
          />
        </Form.Item>
        <Form.Item label="Kurum Kodu">
          <TextInput
            name="institutionReturnCode"
            value={institutionReturnCode}
            onChange={handleInputChange}
            allowClear
          />
        </Form.Item>
        <Form.Item label="Banka Kodu">
          <TextInput
            name="bankReturnCode"
            value={bankReturnCode}
            onChange={handleInputChange}
            allowClear
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
