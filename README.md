  const [currency1, setCurrency1] = useState('');
  const [currency2, setCurrency2] = useState('');
  const [textInput, setTextInput] = useState('');
  const [result, setResult] = useState('');

  const calculateResult = useCallback(() => {
    const sum = Number(currency1) + Number(currency2); 
    const multiplier = Number(textInput);
    if (!isNaN(multiplier)) {
      return sum * multiplier; 
    }
    return 0; 
  }, [currency1, currency2, textInput]);

  useEffect(() => {
    if (currency1 !== '' && currency2 !== '' && textInput !== '') {
      const finalResult = calculateResult(); // useCallback ile hesaplama fonksiyonunu çağırıyoruz
      setResult(finalResult); // Hesaplanan sonucu dördüncü inputa yaz
    }
  }, [currency1, currency2, textInput, calculateResult]);




 <div>
      <input
        type="number"
        value={currency1}
        onChange={(e) => setCurrency1(e.target.value)}
        placeholder="Currency 1"
      />
      <input
        type="number"
        value={currency2}
        onChange={(e) => setCurrency2(e.target.value)}
        placeholder="Currency 2"
      />
      <input
        type="number"
        value={textInput}
        onChange={(e) => setTextInput(e.target.value)}
        placeholder="Multiplier (Text Input)"
      />
      <input
        type="number"
        value={result}
        readOnly
        placeholder="Result"
      />
    </div>
