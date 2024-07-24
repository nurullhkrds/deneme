  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator();

  const fetchInstitutionsData = async (searchCriteria = {}) => {
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

  useEffect(() => {
    fetchInstitutionsData();
  }, []);
