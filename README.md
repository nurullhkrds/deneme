const ReturnMapServiceParametersSearch = ({ callApi }) => {
  const dispatch = useDispatch();
  const [searchCriteria, setSearchCriteria] = useState({
    returnMapCode: '',
    bankReturnCode: '',
    institutionReturnCode: '',
  });

  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setSearchCriteria((prevCriteria) => ({
      ...prevCriteria,
      [name]: value,
    }));
  };

  const handleSearch = () => {
    dispatch(fetchInstitutionsData(dispatch, callApi, searchCriteria)());
  };

  return (
    <Fragment>
      <Form onClick={handleSearch}>
        <Form.Item label="ReturnMap Kodu">
          <TextInput
            name="returnMapCode"
            value={searchCriteria.returnMapCode}
            onChange={handleInputChange}
            allowClear
          />
        </Form.Item>
        <Form.Item label="Kurum Kodu">
          <TextInput
            name="institutionReturnCode"
            value={searchCriteria.institutionReturnCode}
            onChange={handleInputChange}
            allowClear
          />
        </Form.Item>
        <Form.Item label="Banka Kodu">
          <TextInput
            name="bankReturnCode"
            value={searchCriteria.bankReturnCode}
            onChange={handleInputChange}
            allowClear
          />
        </Form.Item>
        
      </Form>
      <Button onClick={handleSearch}>Search</Button>
        <Button
          type="secondary"
          onClick={() => {
            setSearchCriteria({
              returnMapCode: '',
              bankReturnCode: '',
              institutionReturnCode: '',
            });
            dispatch(fetchInstitutionsData(dispatch, callApi, {})());
          }}
        >
          Reset
        </Button>
    </Fragment>
  );
};

export default ReturnMapServiceParametersSearch;
