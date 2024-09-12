const ReturnMapDefinitionServiceParametersSearch = ({ callApi, definitionList }) => {
  const dispatch = useDispatch();
  const ref = useRef(null);



  const returnMapCodeSearch = useSelector((state) => state.returnMap.returnMapCode);
  const [returnMapCode, setReturnMapCode] = useState(returnMapCodeSearch || "");

  useEffect(() => {
    setReturnMapCode(returnMapCodeSearch);
  }, [returnMapCodeSearch]);

  const { i18n } = useIntl();
  const { ReturnMapFormLocale } = i18n;

  const handleDefinitionSearch = () => {
    if (returnMapCode) {
      dispatch(setReturnMapCodeReducer(returnMapCode))
      dispatch(toggleSubTableActive(true))
      dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, returnMapCode));
      dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode: returnMapCode }));
    }
    else {
      dispatch(fetchAllReturnMapDefinition(dispatch, callApi))
    }
    dispatch(toggleSearchTrigger(true))
  };
