import { Modal, Spin } from "ykb-ui";
import InstServiceParameterDetailModalForm from "./InstServiceParameterDetailModalForm";

const InstServiceParameterDetailModal = (props) => {
  const [spinning, setSpinning] = useState({
    status: false,
    text: null,
  });

  const instServiceParameterDetailModalData = useSelector(
    (state) => state.instServiceParameter.instServiceParameterDetailModal
  );

  useEffect(() => {
    console.log(instServiceParameterDetailModalData);
  }, []);

  return (
    <React.Fragment>
      <Modal
        title={instServiceParameterDetailModalData.title}
        visible={instServiceParameterDetailModalData.visible}
        size="large"
        onClose={props.handleClose}
      >
        <Spin spinning={spinning.status} text={spinning.text}>
          <InstServiceParameterDetailModalForm
            handleOk={props.handleOk}
            handleClose={props.handleClose}
            setSpinning={setSpinning}
          />
        </Spin>
      </Modal>
    </React.Fragment>
  );
};

InstServiceParameterDetailModal.propTypes = {
  handleOk: PropTypes.func.isRequired,
  handleClose: PropTypes.func.isRequired,
};

export default InstServiceParameterDetailModal;





function ReturnMapServiceParameters() {
  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator();

  

  useEffect(() => {
    dispatch(fetchInstitutionsData(dispatch,callApi,{}));
  }, []);

  return (
    <>
      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersSearch  callApi={callApi} />
        </Col>
      </Row>
      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersTable />
        </Col>
      </Row>
      <Row style={{ marginTop: "25px" }}>
        <Col xs={24} sm={24} md={24} lg={24}>
          <Button>Ekle</Button>
          <Button>Güncelle</Button>
          <Button>Kopyala</Button>
        </Col>
      </Row>
    </>
  );
}

export default ReturnMapServiceParameters;






"export const sendSaveInstServiceParameterRequest = async (
  callApi,
  instServiceParameter
) => {
  const endpoint = "management/institution/service-parameter";
  try {
    return await callApi({
      method: "POST",
      endpoint: endpoint,
      body: instServiceParameter,
      notifyErrors: false,
    });
  } catch (ex) {
    arrangeExMessageAndThrowEx(ex);
  }
};"
