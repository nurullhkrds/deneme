 const handleClickPromptCustomButtonText = (record) => {
    Message.prompt({
      title: 'Kayıt Silme',
      content: 'Kaydı silmek istediğinize emin misiniz ?',
      icon: <Icon name="warning-circle" colorType="warning" />,
      onClose: () => {
        console.log('onClose');
      },
      onOk: () => {
        console.log('onOk');

        // record.id'yi kullanarak request oluşturuyoruz
        const deleteReturnMapRequest = {
          ids: [record.id]
        };

        sendDeleteReturnMapRequest(callApi, deleteReturnMapRequest)
          .then(() => {
            dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode: 'someCode' }));
            Notification.success('Silme Başarılı', 3);
          })
          .catch(error => {
            console.error('Error creating return map:', error);
            Notification.error('Hatalı silme işlemi !', 3);
          });
      },
      onCancel: () => {
        console.log('onCancel');
      },
      okText: 'Evet',
      cancelText: 'Vazgeç',
    });
  };
