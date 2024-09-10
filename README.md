  const returnMapCodeDefinitionSearch = useSelector((state) => state.returnMap.returnMapCode);
  useEffect(() => {
    setReturnMapCode(returnMapCodeDefinitionSearch)

  }, [returnMapCodeDefinitionSearch])

  const [returnMapCode, setReturnMapCode] = useState(returnMapCodeDefinitionSearch ?returnMapCodeDefinitionSearch :""  );
