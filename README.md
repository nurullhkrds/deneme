const dataWithKeys = returnMapOneData.map(item => ({
  ...item,
  returnMapCode: item.returnMapDefinition?.returnMapCode || item.returnMapCode,
  key: item.id
}));
