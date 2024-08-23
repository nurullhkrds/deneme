       <Form.Item label="ReturnMap Seç">
              <Select onChange={handleSelectChange}>
                {data.map(item => (
                  <Option key={item.id} value={item.id}>
                    {item.returnMapCode}
                  </Option>
                ))}
              </Select>
            </Form.Item>


  useEffect(() => {
    // API'ye istek atarak veriyi alın
    const fetchData = async () => {
      const response = await fetch('API_URL'); // API URL'inizi ekleyin
      const result = await response.json();
      setData(result);
    };
    fetchData();
  }, []);

  const handleSelectChange = (value) => {
    setSelectedId(value);
  };
