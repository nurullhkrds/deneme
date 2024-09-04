     <section>
        <Modal
          title="ReturnMap"
          visible={modalDefinitionVisible}
          onClose={handleCancelForCreateDefinition}
          footer={[
            <SecureButton permission="handleCancelForCreateDefinition" key="cancel" onClick={handleCancelForCreateDefinition}>
              Vazgeç
            </SecureButton>,
            <SecureButton permission="handleOkForCreateDefinition" key="ok" type="primary" onClick={handleOkForCreateDefinition}>
              Kaydet
            </SecureButton>
          ]}
        >

          <div>
            <Form >
              <Form.Item label="Dönüş Kodu">
                <input
                  name="returnMapCode"
                  placeholder='YKB_RETURN_MAP_CODE'
                  value={returnMapDefinition}
                  onChange={handleDefinitionReturnMap}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%', }}
                />
              </Form.Item>
              <Form.Item label="Aktiflik">
                <Checkbox checked={definitionIsActive} onChange={handleDefinitionIsActive} />
              </Form.Item>

            </Form>
          </div>
        </Modal>

      </section>



  const handleOkForCreateDefinition = () => {
    setModalDefinitionVisible(false);

    sendcreateReturnMapDefinitionRequest(callApi, createDefinitionRequest)
      .then(() => {
        dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, { returnMapCode }));
        Notification.success('Ekleme Başarılı', 3);
      })
      .catch(error => {
        Notification.error('Böyle bir veri zaten kayıtlı', 4);

      });

    setDefinitionIsActive(false);
    setReturnMapDefinition("");
  };
