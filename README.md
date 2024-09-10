const returnMapCode = useSelector((state) => state.returnMap.returnMapCode);
const [localReturnMapCode, setLocalReturnMapCode] = useState(returnMapCode || "");

useEffect(() => {
  setLocalReturnMapCode(returnMapCode);
}, [returnMapCode]);
