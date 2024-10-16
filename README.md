
  const [value1, setValue1] = useState(0);
  const [value2, setValue2] = useState(0);
  const [value3, setValue3] = useState(0);
  const [result1, setResult1] = useState(0);
  const [result2, setResult2] = useState(0);
  const [result3, setResult3] = useState(0);
  const [result4, setResult4] = useState(0);
  const [result5, setResult5] = useState(0);

  const calculateResults = useCallback(() => {
    // Girdi değerlerine bağlı olarak hesaplama işlemi
    setResult1(value1 + value2);
    setResult2(value2 * value3);
    setResult3(value1 - value3);
    setResult4(value1 + value2 + value3);
    setResult5(value1 * value2 * value3);
  }, [value1, value2, value3]);
