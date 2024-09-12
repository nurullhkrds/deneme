
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
      const foundDefinition = definitionList.find(
        (definition) => definition.returnMapCode === returnMapCode
      );

      if (foundDefinition && foundDefinition.isActive) {
        dispatch(toggleSubTableActive(true));
      } else {
        dispatch(toggleSubTableActive(false));
      }

      dispatch(setReturnMapCodeReducer(returnMapCode));
      dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, returnMapCode));
      dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode: returnMapCode }));
    } else {
      dispatch(fetchAllReturnMapDefinition(dispatch, callApi));
    }

    dispatch(toggleSearchTrigger(true));
  };
