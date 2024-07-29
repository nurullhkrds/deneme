  const handleClickPromptCustomButtonText = async (record) => {
    const selectedData = await dispatch(fetchReturnMapById(dispatch, callApi, record.id));
    Message.prompt({
      title: 'Kayıt Silme',
      content: 'Kaydı silmek istediğinize emin misiniz ?',
      icon: <Icon name="warning-circle" colorType="warning" />,
      onClose: () => {
        console.log('onClose');
      },
      onOk: () => {
        console.log('onOk');

        const deleteReturnMapRequest = {
          ids: [record.id]
        };
        sendDeleteReturnMapRequest(callApi, deleteReturnMapRequest)
          .then(() => {
            dispatch(fetchReturnMapsData(dispatch, callApi, { selectedData.returnMapCode }));
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
