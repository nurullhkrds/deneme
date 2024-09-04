
const ReturnMapServiceParametersTable = ({ definitionList }) => {
  const returnMapData = useSelector((state) => state.returnMap.list);
  const returnMapOneData = useSelector((state) => state.returnMap.returnMapData);
  const selectedRowKeys = useSelector((state) => state.returnMap.selectedRowKeys);
  const spinnig = useSelector((state) => state.returnMap.spinning);
  
  const [modalVisible, setModalVisible] = useState(false);
  const [returnMapCode, setReturnMapCode] = useState('');
  const [institutionReturnCode, setInstitutionReturnCode] = useState('');
  const [institutionReturnText, setInstitutionReturnText] = useState('');
  const [bankReturnCode, setBankReturnCode] = useState('');
  const [bankReturnText, setBankReturnText] = useState('');
  const [returnType, setReturnType] = useState('');
  const [isReversible, setIsReversible] = useState(false);
  const [definitionId, setDefinitionId] = useState('');
  const [updatedId, setUpdatedId] = useState('');
  const [isChanged, setIsChanged] = useState(false); // Değişiklik kontrolü için eklenen state

  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator();
  const formRef = React.useRef(null);

  const updateData = {
    id: updatedId,
    institutionReturnCode,
    institutionReturnText,
    bankReturnCode,
    bankReturnText,
    returnType,
    isReversible,
    returnMapDefinitionId: definitionId
  };

  // useEffect ile inputlarda bir değişiklik olup olmadığını kontrol et
  useEffect(() => {
    if (formRef.current && returnMapOneData) {
      const {
        returnMapCode = '',
        institutionReturnCode = '',
        institutionReturnText = '',
        bankReturnCode = '',
        bankReturnText = '',
        isReversible = false,
        returnType = '',
        returnMapDefinition = {}
      } = returnMapOneData;

      const newFields = {
        institutionReturnCode,
        institutionReturnText,
        bankReturnCode,
        bankReturnText,
        isReversible,
        returnType,
        definitionId: returnMapDefinition?.id || ''
      };

      formRef.current.setFieldsValue(newFields);

      setReturnMapCode(returnMapCode);
      setInstitutionReturnCode(institutionReturnCode);
      setInstitutionReturnText(institutionReturnText);
      setBankReturnCode(bankReturnCode);
      setBankReturnText(bankReturnText);
      setIsReversible(isReversible);
      setReturnType(returnType);
      setDefinitionId(returnMapDefinition?.id || '');

      // Değişiklik kontrolü
      setIsChanged(
        JSON.stringify(newFields) !== JSON.stringify({
          institutionReturnCode,
          institutionReturnText,
          bankReturnCode,
          bankReturnText,
          isReversible,
          returnType,
          definitionId: returnMapDefinition?.id || ''
        })
      );
    }
  }, [returnMapOneData, institutionReturnCode, institutionReturnText, bankReturnCode, bankReturnText, isReversible, returnType, definitionId]);

  const handleOkForUpdate = () => {
    if (!isChanged) return; // Eğer değişiklik yoksa güncelleme yapılmasın

    setModalVisible(false);
    sendUpdateReturnMapRequest(callApi, updateData)
      .then(() => {
        dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode }));
        Notification.success('Güncelleme Başarılı', 3);
      })
      .catch(error => {
        console.error('Error creating return map:', error);
        Notification.error('Hatalı güncelleme', 3);
      });
  };

  const handleCancelForUpdate = () => {
    setModalVisible(false);
  };

  const handleInstitutionReturnCode = (e) => {
    setInstitutionReturnCode(e.target.value);
  };

  const handleInstitutionReturnText = (e) => {
    setInstitutionReturnText(e.target.value);
  };

  const handleBankReturnCode = (e) => {
    setBankReturnCode(e.target.value);
  };

  const handleBankReturnText = (e) => {
    setBankReturnText(e.target.value);
  };

  const handleSelectReturnType = (value) => {
    setReturnType(value);
  };

  const handleSelectReversible = () => {
    setIsReversible(!isReversible);
  };

  const handleChangeSelectDefinition = (value) => {
    setDefinitionId(value);
  };

  return (
    <>
      <Table
        rowSelection={rowSelection}
        columns={columns}
        data={dataWithKeys}
        loading={{ text: 'Veriler listeleniyor...', status: spinnig }}
      />

      <Modal
        title="ReturnMap"
        visible={modalVisible}
        onClose={handleCancelForUpdate}
        footer={[
          <SecureButton key="cancel" onClick={handleCancelForUpdate}>
            İptal
          </SecureButton>,
          <SecureButton key="ok" type="primary" onClick={handleOkForUpdate} disabled={!isChanged}>
            Kaydet
          </SecureButton>
        ]}
      >
        <Form ref={formRef}>
          {/* Tüm inputlar */}
          <Form.Item label="ReturnMap Seç">
            <select
              id="definitionId"
              name="definitionId"
              value={definitionId}
              onChange={(e) => handleChangeSelectDefinition(e.target.value)}
              style={{ width: '100%', padding: '8px', borderRadius: '4px', border: '1px solid #dcdcdc' }}
            >
              <option value="" selected={definitionId === ""}>Seç</option>
              {definitionList.map(item => (
                <option key={item.id} value={item.id} selected={definitionId === item.id}>
                  {item.returnMapCode}
                </option>
              ))}
            </select>
          </Form.Item>

          <Form.Item label="Kurum Kodu">
            <input
              name="institutionReturnCode"
              value={institutionReturnCode}
              onChange={handleInstitutionReturnCode}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>

          <Form.Item label="Kurum Metni">
            <input
              name="institutionReturnText"
              value={institutionReturnText}
              onChange={handleInstitutionReturnText}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>

          <Form.Item label="Banka Kodu">
            <input
              name="bankReturnCode"
              value={bankReturnCode}
              onChange={handleBankReturnCode}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>

          <Form.Item label="Banka Metni">
            <input
              name="bankReturnText"
              value={bankReturnText}
              onChange={handleBankReturnText}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>

          <Form.Item label="Geri Dönüş Tipi">
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
          </Form.Item>

          <Form.Item>
            <Checkbox textLabel="Geri Dönüşüm" checked={isReversible} onChange={handleSelectReversible} />
          </Form.Item>
        </Form>
      </Modal>
    </>
  );
};

export default ReturnMapServiceParametersTable;
