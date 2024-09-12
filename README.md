const handleDefinitionSearch = () => {
  if (returnMapCode) {
    // definitionList içinde aratılan returnMapCode'yi buluyoruz
    const foundDefinition = definitionList.find(
      (definition) => definition.returnMapCode === returnMapCode
    );

    // Eğer definition bulunduysa ve isActive durumu true ise toggleSubTableActive'i true yap
    if (foundDefinition && foundDefinition.isActive) {
      dispatch(toggleSubTableActive(true));
    } else {
      // Bulunmadıysa veya isActive değilse toggleSubTableActive'i false yap
      dispatch(toggleSubTableActive(false));
    }

    // Diğer işlemleri gerçekleştir
    dispatch(setReturnMapCodeReducer(returnMapCode));
    dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, returnMapCode));
    dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode: returnMapCode }));
  } else {
    // Eğer returnMapCode boş ise tüm kayıtları getir
    dispatch(fetchAllReturnMapDefinition(dispatch, callApi));
  }

  dispatch(toggleSearchTrigger(true));
};
