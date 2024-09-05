useEffect(() => {
  let initialData = {};
  
  if (returnMapDefinitionData) {
    initialData = {
      returnMapCode: returnMapDefinitionData.returnMapCode,
      isActive: returnMapDefinitionData.isActive,
    };
  } else if (definitionList && updatedId) {
    const currentRecord = definitionList.find(item => item.id === updatedId);
    if (currentRecord) {
      initialData = {
        returnMapCode: currentRecord.returnMapCode,
        isActive: currentRecord.isActive,
      };
    }
  }

  const currentData = {
    returnMapCode,
    isActive,
  };

  setIsChanged(JSON.stringify(initialData) !== JSON.stringify(currentData)); // Değişiklik kontrolü
}, [returnMapCode, isActive, returnMapDefinitionData, definitionList, updatedId]);
