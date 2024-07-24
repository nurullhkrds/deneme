const ReturnMapServiceParametersSearch = ({ callApi }) => {
  const dispatch = useDispatch();
  const [searchCriteria, setSearchCriteria] = useState({
    returnMapCode: '',
    bankReturnCode: '',
    institutionReturnCode: '',
  });

  const handleInputChange = (e) => {
    // `e.target`'in varlığını ve gerekli özelliklere sahip olduğunu kontrol et
    if (e && e.target) {
      const { name, value } = e.target;
      setSearchCriteria((prevCriteria) => ({
        ...prevCriteria,
        [name]: value,
      }));
    } else {
      console.warn('Event target is missing or not in expected format:', e);
    }
  };

  const handleSearch = () => {
    dispatch(fetchInstitutionsData(callApi, searchCriteria));
  };

  return (
    <Fragment>
      <Form>
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
        <Button type="primary" onClick={handleSearch}>Search</Button>
        <Button
          type="secondary"
          onClick={() => {
            setSearchCriteria({
              returnMapCode: '',
              bankReturnCode: '',
              institutionReturnCode: '',
            });
            dispatch(fetchInstitutionsData(callApi, {}));
          }}
        >
          Reset
        </Button>
      </Form>
    </Fragment>
  );
};

export default ReturnMapServiceParametersSearch;
