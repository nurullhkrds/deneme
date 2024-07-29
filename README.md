  const [searchCriteria, setSearchCriteria] = useState({});

  const handleSearchCriteria = (criteria) => {
    setSearchCriteria(criteria);
    // burada criteria ile ilgili başka işlemler yapabilirsiniz
  };





   // searchCriteria değiştiğinde callback fonksiyonunu çağırın
    onSearchCriteriaChange({
      returnMapCode: name === 'returnMapCode' ? value : returnMapCode,
      bankReturnCode: name === 'bankReturnCode' ? value : bankReturnCode,
      institutionReturnCode: name === 'institutionReturnCode' ? value : institutionReturnCode,
    });
  };
