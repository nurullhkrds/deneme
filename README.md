const dataWithKeys = useMemo(() => 
  returnMapData.map((item, index) => ({
    ...item,
    key: index + 1,
    returnMapCode: item.returnMapDefinition?.returnMapCode || item.returnMapCode
  })), 
  [returnMapData]
);
