  const handleClickOneDefinitionDelete = async (record) => {
    try {
      const institutions = record.institutions || [];
      let contentMessage;

      if (institutions.length === 1) {
        contentMessage = `${institutions[0]} kurumu bu returnMap'i kullanmakta. Silmek istediğinize emin misiniz?`;
      } else if (institutions.length > 1) {
        contentMessage = `${institutions.join(', ')} kurumları bu returnMap'i kullanmakta. Silmek istediğinize emin misiniz?`;
      } else {
        contentMessage = ReturnMapFormLocale.messages.deleteContent;
      }

      Message.prompt({
        title: ReturnMapFormLocale.messages.deleteTitle,
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

              if (returnMapCodeControl) {
                dispatch(toggleSearchTrigger(false))
                dispatch(setReturnMapDefinitionData(null))

                dispatch(setReturnMapCodeReducer(null))
                setDataList([])
                dispatch(fetchAllReturnMapDefinition(dispatch, callApi))
                dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode: record.returnMapCode }));
                dispatch(fetchAllReturnMapDefinitionWithIsActiveTrue(dispatch, callApi))

              }
              else {
                dispatch(fetchAllReturnMapDefinition(dispatch, callApi))
                dispatch(fetchAllReturnMapDefinitionWithIsActiveTrue(dispatch, callApi))

              }

              Notification.success(ReturnMapFormLocale.messages.deleteSuccess, 5);
            })
            .catch((error) => {
              console.error('Error creating return map:', error);
              Notification.error(ReturnMapFormLocale.messages.deleteError, 5);
            });
        },
        onCancel: () => {
          console.log('onCancel');
        },
        okText: ReturnMapFormLocale.deleteButton,
        cancelText: ReturnMapFormLocale.cancelButton,
      });
    } catch (error) {
      console.error('Error fetching return map by id:', error);
    }
  };



    Message.warning({
        title: ReturnMapFormLocale.messages.deleteTitle,
        content: contentMessage,
      });
