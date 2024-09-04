const ReturnMapDefinitionServiceParametersTable = () => {
  const returnMapDefinitionData = useSelector((state) => state.returnMap.returnMapDefinition);
  const spinnig = useSelector((state) => state.returnMap.spinning);

  const formRef = React.useRef(null);

  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator();
  const [modalVisible, setModalVisible] = useState(false);
  const [returnMapCode, setReturnMapCode] = useState();
  const [isActive, setIsActive] = useState();
  const [updatedId, setUpdatedId] = useState();
  const [dataList, setDataList] = useState([]);
  const [isChanged, setIsChanged] = useState(false); // Değişiklik kontrolü için eklenen state

  const updateData = {
    id: updatedId,
    returnMapCode: returnMapCode,
    isActive: isActive
  };

  useEffect(() => {
    if (returnMapDefinitionData) {
      setDataList([returnMapDefinitionData]);
    } else {
      setDataList([]);
    }
  }, [returnMapDefinitionData]);

  useEffect(() => {
    const initialData = {
      returnMapCode: returnMapDefinitionData?.returnMapCode,
      isActive: returnMapDefinitionData?.isActive,
    };
  
    const currentData = {
      returnMapCode,
      isActive,
    };

    setIsChanged(JSON.stringify(initialData) !== JSON.stringify(currentData)); // Değişiklik kontrolü
  }, [returnMapCode, isActive, returnMapDefinitionData]);

  const handleDefinitionEdit = async (record) => {
    showModal();
    setReturnMapCode(record.returnMapCode);
    setIsActive(record.isActive);
    setUpdatedId(record.id);
  };

  const showModal = () => {
    setModalVisible(true);
  };

  const handleOkForDefinitionUpdate = (record) => {
    if (!isChanged) return; // Eğer değişiklik yoksa güncelleme işlemi yapılmaz

    setModalVisible(false);
    sendUpdateReturnMapDefinitionRequest(callApi, updateData)
      .then(() => {
        dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, { returnMapCode }));
        Notification.success('Güncelleme Başarılı', 3);
      })
      .catch(error => {
        Notification.error('Hatalı güncelleme! Böyle bir kayıt zaten var.', 5);
      });
  };

  const handleCancelDefinitionForUpdate = () => {
    setModalVisible(false);
  };

  const handleReturnMapCode = (e) => {
    setReturnMapCode(e.target.value);
  };

  const handleIsActive = value => {
    setIsActive(!isActive);
  };

  const columns = [
    {
      title: 'Dönüş Kodu',
      dataIndex: 'returnMapCode',
      key: 'returnMapCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Aktif',
      dataIndex: 'isActive',
      key: 'isActive',
      width: 200,
      render: value => (value ? 'Evet' : 'Hayır'),
      resizable: true,
    },
    {
      key: "edit",
      width: 5,
      align: "center",
      render: (text, record) => (
        <SecureButton
          type="primary"
          size="small"
          title={"Güncelle"}
          permission="handleDefinitionEdit"
          onClick={() => handleDefinitionEdit(record)}
        >
          <Icon name="edit" size="small" />
        </SecureButton>
      ),
    },
    {
      key: 'actions',
      width: 5,
      align: "center",
      render: (text, record) => (
        <DeleteButton
          type="danger"
          size="small"
          title={"Sil"}
          permission="handleClickOneDefinitionDelete"
          onClick={() => {
            handleClickOneDefinitionDelete(record);
          }}
        >
          <Icon name="trash" size="small" />
        </DeleteButton>
      )
    }
  ];

  const handleClickOneDefinitionDelete = async (record) => {
    try {
      const institutions = record.institutions;
      let contentMessage;

      if (institutions.length === 1) {
        contentMessage = `${institutions[0]} kurumu bu returnMap'i kullanmakta. Silmek istediğinize emin misiniz?`;
      } else if (institutions.length > 1) {
        contentMessage = `${institutions.join(', ')} kurumları bu returnMap'i kullanmakta. Silmek istediğinize emin misiniz?`;
      } else {
        contentMessage = 'Kaydı silmek istediğinize emin misiniz?';
      }
      Message.prompt({
        title: 'Kayıt Silme',
        content: contentMessage,
        icon: <Icon name="warning-circle" colorType="warning" />,
        onClose: () => {
          console.log('onClose');
        },
        onOk: () => {
          const deleteReturnMapDefinitionRequest = {
            ids: [record.id]
          };
          sendDeleteReturnMapDefinitionRequest(callApi, deleteReturnMapDefinitionRequest)
            .then(() => {
              dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, { returnMapCode }));
              Notification.success('Silme Başarılı', 3);
              dispatch(setReturnMapDefinitionData(null));
            })
            .catch(error => {
              console.error('Error creating return map:', error);
              Notification.error('Hatalı silme işlemi!', 3);
            });
        },
        onCancel: () => {
          console.log('onCancel');
        },
        okText: 'Evet',
        cancelText: 'Vazgeç',
      });
    } catch (error) {
      console.error('Error fetching return map by id:', error);
    }
  };

  return (
    <>
      <Table
        columns={columns}
        data={dataList}
        loading={{ text: 'Veriler listeleniyor...', status: spinnig }}
      />

      <section>
        <Modal
          title="ReturnMap"
          visible={modalVisible}
          onClose={handleCancelDefinitionForUpdate}
          footer={[
            <SecureButton permission="handleCancelDefinitionForUpdate" key="cancel" onClick={handleCancelDefinitionForUpdate}>
              İptal
            </SecureButton>,
            <SecureButton permission="handleOkForDefinitionUpdate" key="ok" type="primary" onClick={handleOkForDefinitionUpdate} disabled={!isChanged}>
              Kaydet
            </SecureButton>
          ]}
        >
          <div>
            <Form ref={formRef}>
              <Form.Item label="Dönüş Kodu">
                <input
                  name="returnMapCode"
                  value={returnMapCode}
                  onChange={handleReturnMapCode}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
                />
              </Form.Item>

              <Form.Item label="Aktiflik">
                <Checkbox checked={isActive} onChange={handleIsActive} />
              </Form.Item>
            </Form>
          </div>
        </Modal>
      </section>
    </>
  );
};

export default ReturnMapDefinitionServiceParametersTable;
