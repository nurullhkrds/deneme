route dosyam:
const routes = [

  {
    exact: true,
    processId: Pages.instServiceParameters.processId,
    path: Pages.instServiceParameters.path,
    component: asyncComponent(() =>
      import("./pages/instServiceParameter/InstServiceParametersWrapper")
    ),
    i18n: (lang) =>
      import(
        /* webpackMode: "lazy" */ `./i18n/instServiceParameter/InstServiceParameters-${lang}.js`
      ),
  },
];


const InstServiceParameters = () => {
  const { callApi } = useShellCommunicator();
  const dispatch = useDispatch();
  const instServiceParameterSearchFormRef = useRef(null);

  const spinning = useSelector((state) => state.commonUsage.spinning);

  const instServiceParameterDetailModalData = useSelector(
    (state) => state.instServiceParameter.instServiceParameterDetailModal
  );

  const mapFromSearchResponseToInstServiceParameters = (searchInstServiceParameterResponse) => {
    return searchInstServiceParameterResponse.map((instServiceParameter) => {
      return {
        id: instServiceParameter.id,
        institutionId: instServiceParameter.institutionId,
        parameterGroupCode: instServiceParameter.parameterGroupCode,
        parameterKey: instServiceParameter.parameterKey,
        parameterValue: instServiceParameter.parameterValue,
        description: instServiceParameter.description,
        isActive: instServiceParameter.isActive ? "Aktif" : "Pasif",
      };
    });
  };

  const searchHandler = async (criteria) => {
    try {
      dispatch(
        commonUsageActions.setSpinning({
          status: true,
          text: "Lütfen Bekleyiniz...",
        })
      );

      let searchInstServiceParameterResponse =
        await sendSearchInstServiceParameterRequest(callApi, criteria);

      const instServParamList = mapFromSearchResponseToInstServiceParameters(
        searchInstServiceParameterResponse
      );

      dispatch(
        instServiceParameterActions.setInstServiceParameterList(instServParamList)
      );

      dispatch(
        commonUsageActions.setSpinning({
          status: false,
          text: null,
        })
      );
    } catch (ex) {
      dispatch(instServiceParameterActions.setInstServiceParameterList([]));
      dispatch(
        commonUsageActions.setSpinning({
          status: false,
          text: null,
        })
      );
    }
  };

  const prepareSearchFilters = (formData) => {
    return {
      parameterGroupCode: isEmpty(formData.parameterGroupCode)
        ? undefined
        : formData.parameterGroupCode,
      parameterKey: isEmpty(formData.parameterKey)
        ? undefined
        : formData.parameterKey,
      parameterValue: isEmpty(formData.parameterValue)
        ? undefined
        : formData.parameterValue,
      description: isEmpty(formData.description)
        ? undefined
        : formData.description,
      isActive:
        isEmpty(formData.isActive) || formData.isActive === "all"
          ? undefined
          : formData.isActive === "active"
            ? true
            : false,
    };
  };

  const onInstServiceParameterDetailModalOk = () => {
    const searchRequest = prepareSearchFilters(
      instServiceParameterSearchFormRef.current.getFieldsValue()
    );
    searchHandler(searchRequest);
    dispatch(
      instServiceParameterActions.resetInstServiceParameterDetailModalData()
    );
  };

  const onInstServiceParameterDetailModalClose = () => {
    dispatch(
      instServiceParameterActions.resetInstServiceParameterDetailModalData()
    );
  };

  const onInstServiceParameterDetailModalDelete = () => {
    const searchRequest = prepareSearchFilters(
      instServiceParameterSearchFormRef.current.getFieldsValue()
    );
    searchHandler(searchRequest);
    dispatch(
      instServiceParameterActions.resetInstServiceParameterDetailModalData()
    );
  };

  return (
    <Fragment>
      <Spin spinning={spinning.status} text={spinning.text}>
        <Row>
          <Col xs={24} sm={24} md={24} lg={24}>
            <InstServiceParameterSearchForm
              setFormRef={(ref) => {
                instServiceParameterSearchFormRef.current = ref.current;
              }}
              onSearchClick={searchHandler}
            />
          </Col>
        </Row>
        <Row>
          <Col xs={24} sm={24} md={24} lg={24}>
            <InstServiceParametersTable />
          </Col>
        </Row>
        {instServiceParameterDetailModalData && (
          <InstServiceParameterDetailModal
            handleOk={onInstServiceParameterDetailModalOk}
            handleClose={onInstServiceParameterDetailModalClose}
            handleDelete={onInstServiceParameterDetailModalDelete}
          />
        )}
      </Spin>
    </Fragment>
  );
}

export default InstServiceParameters;

bu bir componenttim ve bunun gibi nice compnonentim var... 



export default {
  instServiceParameters: {
    code: "GSMPRESRVPRMDEF",
    name: "GSM Kurum Servis Parametre Tanımlama",
    processId: 5182,
    path: "/serviceParameters",
  },
}; bu path ve proccesId tarafı 





export const sendGetInstitutionsRequest = async (callApi) => {
  const endpoint = "management/institution";
  try {
    return await callApi({
      method: "GET",
      endpoint: endpoint,
    });
  } catch (ex) {
    arrangeExMessageAndThrowEx(ex);
  }
};

export const sendSearchInstServiceParameterRequest = async (
  callApi,
  filters
) => {
  const endpoint = "management/institution/service-parameter/search";
  const criteria = filters ?? {};
  const url = buildQueryString(endpoint, criteria);
  try {
    return await callApi({
      method: "GET",
      endpoint: url,
    });
  } catch (ex) {
    arrangeExMessageAndThrowEx(ex);
  }
};

export const sendGetInstServiceParameterRequest = async (callApi, id) => {
  const endpoint = `management/institution/service-parameter/${id}`;
  try {
    return await callApi({
      method: "GET",
      endpoint: endpoint,
    });
  } catch (ex) {
    arrangeExMessageAndThrowEx(ex);
  }
};

export const sendSaveInstServiceParameterRequest = async (
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
};

export const sendUpdateInstServiceParameterRequest = async (callApi, id, instServiceParameter) => {
  const endpoint = `management/institution/service-parameter/update/${id}`;
  try {
    return await callApi({
      method: "PUT",
      endpoint: endpoint,
      body: instServiceParameter,
      notifyErrors: true,
    });
  } catch (ex) {
    arrangeExMessageAndThrowEx(ex);
  }
};

export const sendDeleteInstServiceParameterRequest = async (callApi, id) => {
  const endpoint = `management/institution/service-parameter/delete/${id}`;
  try {
    return await callApi({
      method: "DELETE",
      endpoint: endpoint,
      notifyErrors: true,
    });
  } catch (ex) {
    arrangeExMessageAndThrowEx(ex);
  }
};

 buda benim api dosyam..
