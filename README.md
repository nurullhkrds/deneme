const ReturnMapDefinitionServiceParametersSearch = ({ callApi }) => {
  const dispatch = useDispatch();
  const ref = useRef(null)
  const [returnMapCode, setReturnMapCode] = useState('');
  const definitionList = useSelector((state) => state.returnMap.returnMapDefinitionList);

  const handleInputChange = (e) => {
    const { name, value } = e.target || {};
    if (name === 'returnMapCode') {
      setReturnMapCode(value);
    }


  };


  const handleDefinitionSearch = () => {
    if (returnMapCode) {
      dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, returnMapCode));
    }
  };

  const handleDefinitionReset = () => {

    setReturnMapCode('');
    if (ref.current) {
      ref.current.setFieldsValue("")
    }
    dispatch(setReturnMapDefinitionData(null))

  };
  const handleChangeSelectDefinitionId = (value) => {
    console.log(value)
  }
  return (
    <div >
      <Form ref={ref} >
        <Form.Item colSpan={{ xs: 24, sm: 24, md: 6, lg: 6 }}
          label="Dönüş Kodu"  >
          <input
            name="returnMapCode"
            value={returnMapCode}
            onChange={handleInputChange}
            style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}
          />


        </Form.Item>

        <Form.Item label="" colSpan={{ xs: 24, sm: 24, md: 6, lg: 6 }}
        >
          <SecureButton
            type="secondary"
            permission="handleDefinitionReset"
            onClick={handleDefinitionReset}
          >
            Temizle
          </SecureButton>
          <SecureButton type="primary" permission="handleDefinitionSearch" onClick={handleDefinitionSearch}>
            Listele

          </SecureButton>


        </Form.Item>

      </Form>

    </div>



  );
};

export default ReturnMapDefinitionServiceParametersSearch;
