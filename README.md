  const handleOkForDefinitionUpdate = (record) => {
    setModalVisible(false);
    sendUpdateReturnMapDefinitionRequest(callApi, updateData)
      .then(() => {
        dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, { returnMapCode }));
        Notification.success('Güncelleme Başarılı', 3);
      })
      .catch(error => {
        Notification.error('Hatalı güncelleme ! Böyle bir kayıt zaten var.', 5);
      });
  }; 
