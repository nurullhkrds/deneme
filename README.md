const handleOkForCreate = () => {
  if (!definitionId) {
    Notification.error(ReturnMapFormLocale.messages.createSelectDefinition);
    return;
  }
  const definition = definitionList.find(definition => definition.id === definitionId);

  if (!institutionReturnCode || !bankReturnCode || !institutionReturnText || !bankReturnText) {
    Notification.error(ReturnMapFormLocale.messages.createFullAlan);
    return;
  }

  setModalVisible(false);

  sendcreateReturnMapRequest(callApi, createRequest)
    .then(() => {
      dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode: definition.returnMapCode }));
      Notification.success(ReturnMapFormLocale.messages.createSuccessMessage, 5);
    })
    .catch(error => {
      const errorMessage = error?.response?.data?.exceptionData?.errorMessage || ReturnMapFormLocale.messages.createErrorMessage;
      const errorCode = error?.response?.data?.exceptionData?.errorCode;
      console.error(`Error creating return map (code ${errorCode}):`, error);
      
      // Display the error message from API or a default message
      Notification.error(errorMessage, 5);
    });

  setbankReturnCode("");
  setIsReversible(false);
  setReturnMapCode("");
  setbankReturnText("");
  setinstitutionReturnCode("");
  setinstitutionReturnText("");
};
