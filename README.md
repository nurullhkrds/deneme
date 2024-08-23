const handleOkForCreateDefinition = () => {
  setModalDefinitionVisible(false);

  sendcreateReturnMapDefinitionRequest(callApi, createDefinitionRequest)
    .then(() => {
      dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, { returnMapCode }));
      Notification.success('Ekleme Başarılı', 3);
    })
    .catch(error => {
      // Hata durumunda backend'den gelen mesajı yakala
      if (error.response && error.response.data && error.response.data.message) {
        // Backend'den dönen mesajı notification'a bas
        Notification.error(error.response.data.message, 3);
      } else {
        // Eğer özel bir mesaj yoksa genel bir hata mesajı göster
        Notification.error('Hatalı Ekleme', 3);
      }
      console.error('Error creating return map:', error);
    });

  setDefinitionIsActive(false);
  setReturnMapDefinition("");
};
