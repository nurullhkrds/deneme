const ReturnMapServiceParametersSearch = ({ callApi }) => {
  const dispatch = useDispatch();
  const ref = useRef(null)
  const [returnMapCode, setReturnMapCode] = useState('');
  const [bankReturnCode, setBankReturnCode] = useState('');
  const [institutionReturnCode, setInstitutionReturnCode] = useState('');
  const searchCriteria = {
    returnMapCode,
    bankReturnCode,
    institutionReturnCode,
  };
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


    if (!returnMapCode && !bankReturnCode && !institutionReturnCode) {

    } else {
      dispatch(fetchReturnMapsData(dispatch, callApi, searchCriteria));
    }
  };
  const handleReset = () => {

    setReturnMapCode('');
    setBankReturnCode('');
    setInstitutionReturnCode('');
    if (ref.current) {
      ref.current.setFieldsValue("")
    }
    dispatch(setReturnMapList([]));
  };

  return (
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
      <Button onClick={handleSearch}>
        <IconBox name={"search"} />

      </Button>
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








function ReturnMapServiceParameters() {
  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator();


  return (
    <>

      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersSearch callApi={callApi} />
        </Col>
      </Row>


      <Row style={{ marginTop: '15px ' }}>

        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersTable />
        </Col>
      </Row>

      <Row style={{ marginTop: "25px" }}>
        <Col xs={24} sm={24} md={24} lg={24}>
          <Button type="primary" onClick={showModal}>Ekle</Button>
          {
            selectedIds.length > 0 && (
              <Button onClick={showModalCopy}>Kopyala</Button>
            )
          }
          {
            selectedIds.length > 0 && (
              <Button type="danger" onClick={handleClickPromptCustomButtonText}>
                {`(${selectedIds?.length}) Sil`}
              </Button>
            )
          }


        </Col>
      </Row>


    </>
  );
}

export default ReturnMapServiceParameters;


