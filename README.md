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

      // Aktiflik durumunu hemen dispatch et
      dispatch(toggleSubTableActive(!!(foundDefinition && foundDefinition.isActive)));

      // Diğer işlemleri yap
      dispatch(setReturnMapCodeReducer(returnMapCode));
      dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, returnMapCode));
      dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode: returnMapCode }));
    } else {
      // Eğer returnMapCode boşsa tüm ReturnMapDefinition'ları getir
      dispatch(fetchAllReturnMapDefinition(dispatch, callApi));
    }

    // Arama tetikleyiciyi aç
    dispatch(toggleSearchTrigger(true));
  };
};
