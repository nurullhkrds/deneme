 @Override
    public DataResult<ReturnMapDefinitionDTO> createReturnMapDefinition(CreateReturnMapDefinitionRequest request) {

        Optional<ReturnMapDefinition> existingReturnMap = returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode());

        if (existingReturnMap.isPresent()) {
            return new ErrorDataResult<>(ResultConstant.RECORD_ALREADY_EXISTS.getMessage(), null, 400);
        }

        ReturnMapDefinition newReturnMap = new ReturnMapDefinition();
        newReturnMap.setReturnMapCode(request.getReturnMapCode());
        newReturnMap.setIsActive(request.getIsActive());

        ReturnMapDefinition savedData = returnMapDefinitionRepository.save(newReturnMap);

        ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(savedData);

        if (dto == null) {
            return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
        }

        return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), dto,200);
    }

bu backendim benim 



export const sendcreateReturnMapDefinitionRequest = async (callApi, createReturnMapDefinitionRequest) => {
  const endpoint = "returnMapDefinitions/createReturnMapDefinition";

  try {
    return await callApi({
      method: "POST",
      endpoint: endpoint,
      body: createReturnMapDefinitionRequest,
      notifyErrors: true,
    });
  } catch (ex) {
    console.error('API error:', ex);
    throw ex;
  }
};

 const handleOkForCreateDefinition = () => {

    setModalDefinitionVisible(false);

    sendcreateReturnMapDefinitionRequest(callApi, createDefinitionRequest)
      .then(() => {
        dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, { returnMapCode }));
        Notification.success('Ekleme Başarılı', 3);
      })
      .catch(error => {
        console.error('Error creating return map:', error);
        Notification.error('Hatalı Ekleme', 3);
      });

    setDefinitionIsActive(false);
    setReturnMapDefinition("")

  }
