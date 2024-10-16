  const [value1, setValue1] = useState('');
  const [value2, setValue2] = useState('');
  const [value3, setValue3] = useState('');
  const [result, setResult] = useState('');

  // Hesaplama fonksiyonunu useCallback ile optimize ediyoruz
  const calculateResult = useCallback(() => {
    const sum = Number(value1) + Number(value2);
    return sum * Number(value3);
  }, [value1, value2, value3]);

  // Inputlara değer girildiğinde ve 3 input dolduğunda otomatik hesaplama yap
  useEffect(() => {
    if (value1 !== '' && value2 !== '' && value3 !== '') {
      const finalResult = calculateResult(); // useCallback ile hesaplama fonksiyonunu çağırıyoruz
      setResult(finalResult); // Hesaplanan sonucu dördüncü inputa yaz
    }
  }, [value1, value2, value3, calculateResult]);
