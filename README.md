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


export const sendDeleteReturnMapRequest = async (callApi, deleteReturnMapRequest) => {
  const endpoint = "returnMaps/delete";

  try {
    return await callApi({
      method: "DELETE",
      endpoint: endpoint,
      body: deleteReturnMapRequest,
      notifyErrors: true,
    });
  } catch (ex) {
    console.error('API error:', ex);
    throw ex;
  }
};




@Getter
@Setter
public class DeleteIdsRequest {
    private List<Long> ids;
}
