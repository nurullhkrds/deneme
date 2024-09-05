 const handleClickOneDefinitionDelete = async (record) => {
    try {
      const institutions = record.institutions || [];
      let contentMessage;

      if (institutions.length === 1) {
        contentMessage = `${institutions[0]} kurumu bu returnMap'i kullanmakta. Silmek istediğinize emin misiniz?`;
      } else if (institutions.length > 1) {
        contentMessage = `${institutions.join(', ')} kurumları bu returnMap'i kullanmakta. Silmek istediğinize emin misiniz?`;
      } else {
        contentMessage = 'Kaydı silmek istediğinize emin misiniz?';
      }

      Message.prompt({
        title: 'Kayıt Silme',
        content: contentMessage,
        icon: <Icon name="warning-circle" colorType="warning" />,
        onClose: () => {
          console.log('onClose');
        },
        onOk: () => {
          const deleteReturnMapDefinitionRequest = {
            ids: [record.id],
          };
          sendDeleteReturnMapDefinitionRequest(callApi, deleteReturnMapDefinitionRequest)
            .then(() => {
              dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, { returnMapCode }));
              Notification.success('Silme Başarılı', 5);
              dispatch(setReturnMapDefinitionData(null));
            })
            .catch((error) => {
              console.error('Error creating return map:', error);
              Notification.error('Hatalı silme işlemi!', 5);
            });
        },
        onCancel: () => {
          console.log('onCancel');
        },
        okText: 'Evet',
        cancelText: 'Vazgeç',
      });
    } catch (error) {
      console.error('Error fetching return map by id:', error);
    }
  };
