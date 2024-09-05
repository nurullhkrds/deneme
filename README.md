  useEffect(() => {
    if (returnMapDefinitionData) {
      setDataList([returnMapDefinitionData]);
    } else {
      setDataList(definitionList);
    }
  }, [returnMapDefinitionData, definitionList]);
