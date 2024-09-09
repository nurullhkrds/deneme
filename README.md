 const returnMapCodeDefinitionSearch = useSelector((state) => state.returnMap.returnMapCode);

  useEffect(() => {

  }, [returnMapCodeDefinitionSearch])

  const ref = useRef(null)
  const [returnMapCode, setReturnMapCode] = useState(returnMapCodeDefinitionSearch);
