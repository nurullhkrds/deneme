<section>
  <Modal
    title="ReturnMap"
    visible={modalVisible}
    onClose={handleCancelForCreate}
    footer={[
      <SecureButton permission="handleCancelForCreate" key="cancel" onClick={handleCancelForCreate}>
        Vazgeç
      </SecureButton>,
      <SecureButton permission="handleOkForCreate" key="ok" type="primary" onClick={handleOkForCreate}>
        Kaydet
      </SecureButton>
    ]}
  >
    <div>
      <Form>
        <Form.Item label="Dönüş Kodu Seç" rules={[{ required: true, message: 'Lütfen bir dönüş kodu seçin!' }]}>
          <Select onChange={handleChangeSelectDefinitionId}>
            {definitionListIsActiveTrue?.map((item) => (
              <Option key={item.id} value={item.id}>
                {item.returnMapCode}
              </Option>
            ))}
          </Select>
        </Form.Item>

        <Form.Item label="Kurum Kodu">
          <input
            name="institutionReturnCode"
            value={institutionReturnCode}
            onChange={(e) => {
              const value = e.target.value;
              if (/^[A-Za-z]*$/.test(value)) {
                handleInstitutionReturnCode(e); // Sadece harfler olduğunda state'i güncelle
              } else {
                alert("Kurum Kodu alanına sadece harf girebilirsiniz!");
              }
            }}
            style={{
              border: "1px solid #dcdcdc",
              borderRadius: "4px",
              padding: "8px",
              fontSize: "14px",
              width: "100%",
            }}
          />
        </Form.Item>

        <Form.Item label="Kurum Metni">
          <input
            name="institutionReturnText"
            value={institutionReturnText}
            onChange={handleInstitutionReturnText}
            style={{
              border: "1px solid #dcdcdc",
              borderRadius: "4px",
              padding: "8px",
              fontSize: "14px",
              width: "100%",
            }}
          />
        </Form.Item>

        <Form.Item label="Banka Kodu">
          <input
            name="bankReturnCode"
            value={bankReturnCode}
            onChange={(e) => {
              const value = e.target.value;
              if (/^[A-Za-z]*$/.test(value)) {
                handleBankReturnCode(e); // Sadece harfler olduğunda state'i güncelle
              } else {
                alert("Banka Kodu alanına sadece harf girebilirsiniz!");
              }
            }}
            style={{
              border: "1px solid #dcdcdc",
              borderRadius: "4px",
              padding: "8px",
              fontSize: "14px",
              width: "100%",
            }}
          />
        </Form.Item>

        <Form.Item label="Banka Metni">
          <input
            name="bankReturnText"
            value={bankReturnText}
            onChange={handleBankReturnText}
            style={{
              border: "1px solid #dcdcdc",
              borderRadius: "4px",
              padding: "8px",
              fontSize: "14px",
              width: "100%",
            }}
          />
        </Form.Item>

        <Form.Item label="Geri Dönüş Tipi">
          <Select onChange={handleSelectReturnType}>
            <Option value="SUCCESS">Başarılı</Option>
            <Option value="ERROR">Başarısız</Option>
          </Select>
        </Form.Item>

        <Form.Item>
          <Checkbox textLabel="Geri Dönüşüm" checked={isReversible} onChange={handleSelectReversible} />
        </Form.Item>
      </Form>
    </div>
  </Modal>
</section>
