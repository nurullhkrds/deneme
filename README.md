
  const handleDefinitionSearch = () => {
    if (!returnMapCode) {
    } else {
      dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, returnMapCode));
    }


  };
Empty block statement.
