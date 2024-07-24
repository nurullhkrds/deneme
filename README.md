const handleInputChange = (e) => {
  const { name, value } = e.target || {}; // Güvenli bir şekilde destructure et
  if (name === 'returnMapCode') {
    setReturnMapCode(value);
  } else if (name === 'bankReturnCode') {
    setBankReturnCode(value);
  } else if (name === 'institutionReturnCode') {
    setInstitutionReturnCode(value);
  }
};



// api.js (veya uygun bir dosya adı)
export const fetchInstitutionsData = (dispatch, callApi, searchCriteria = {}) => async () => {
  dispatch(setSpinning(true));
  try {
    let response = await sendSearchReturnMapRequest(callApi, searchCriteria);
    dispatch(setReturnMapList(response.data));
  } catch (error) {
    dispatch(setError(error));
    console.error("fetchInstitutionsData", error);
  } finally {
    dispatch(setSpinning(false));
  }
};


const ReturnMapServiceParametersSearch = ({ callApi }) => {
  const dispatch = useDispatch();
  const [returnMapCode, setReturnMapCode] = useState('');
  const [bankReturnCode, setBankReturnCode] = useState('');
  const [institutionReturnCode, setInstitutionReturnCode] = useState('');

  const handleInputChange = (e) => {
    const { name, value } = e.target || {};
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
    
    // Tüm inputlar boşsa tüm verileri getirecek şekilde düzenleyelim
    if (!returnMapCode && !bankReturnCode && !institutionReturnCode) {
      // Eğer tüm inputlar boşsa, tüm verileri getirmek için arama yapalım
      dispatch(fetchInstitutionsData(dispatch, callApi, {}));
    } else {
      dispatch(fetchInstitutionsData(dispatch, callApi, searchCriteria));
    }
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
