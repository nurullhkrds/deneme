      onOk: () => {
        console.log('onOk');
        sendDeleteReturnMapRequest(callApi, {})
          .then(() => {
            dispatch(fetchReturnMapsData(dispatch, callApi, { returnMapCode }));
            Notification.success('Silme Başarılı', 3);
          })
          .catch(error => {
            console.error('Error creating return map:', error);
            Notification.error('Hatalı silme işlemi !', 3);
          });


      },
