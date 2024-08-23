const handleOkForCreateDefinition = () => {
  setModalDefinitionVisible(false);

  sendcreateReturnMapDefinitionRequest(callApi, createDefinitionRequest)
    .then(() => {
      dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, { returnMapCode }));
      Notification.success('Ekleme Başarılı', 3);
    })
    .catch(error => {
      // Hata durumunda backend'den gelen mesajı yakala
      if (error.response && error.response.status === 400) {
        // 400 hatası durumunda özel mesajı göster
        Notification.error('Böyle bir veri zaten kayıtlı', 3);
      } else if (error.response && error.response.data && error.response.data.message) {
        // Diğer hatalar için backend'den dönen mesajı göster
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
