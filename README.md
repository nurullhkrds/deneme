const handleDefinitionSearch = () => {
  if (returnMapCode) {
    dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, returnMapCode));
  }
};
