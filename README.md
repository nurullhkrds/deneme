  const handleOkForCreate = () => {
    if (!definitionId) {
      Notification.error(ReturnMapFormLocale.messages.createSelectDefinition);
      return;
    }
    const definition = definitionList.find(definition => definition.id === definitionId)


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
        console.error('Error creating return map:', error);
        Notification.error(ReturnMapFormLocale.messages.createErrorMessage, 5);
      });

    setbankReturnCode("");
    setIsReversible(false);
    setReturnMapCode("");
    setbankReturnText("");
    setinstitutionReturnCode("");
    setinstitutionReturnText("");
  };


{
  "exceptionData": {
    "applicationName": "ReturnMapService",
    "errorCode": 409,
    "errorMessage": "Bu Returnmap için aynı kurum hata kodu zaten tanımlı ",
    "traceId": null
  },
  "parameters": {}
}
