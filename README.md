<Row>
  <Col xs={24} sm={24} md={24} lg={24}>
    <ReturnMapDefinitionServiceParametersSearch callApi={callApi} />
  </Col>
</Row>

<div style={{ border: '1px solid #d9d9d9', background: "white", padding: '7px', borderRadius: '4px', marginTop: '15px', display: 'flex', justifyContent: 'space-between', boxShadow: '0 2px 8px rgba(0, 0, 0, 0.1)' }}>
  <h5 style={{ marginTop: "10px", textAlign: "center" }}>Hata Kodları Definition Parametreleri</h5>
  <SecureButton permission="showModalDefinitionCreate" type="primary" onClick={showModalDefinitionCreate}>Ekle</SecureButton>
</div>

<Row>
  <Col xs={24} sm={24} md={24} lg={24}>
    <ReturnMapDefinitionServiceParametersTable />
  </Col>
</Row>

{/* CSS ile ayırma */}
<div style={{ margin: '30px 0', height: '2px', backgroundColor: '#d9d9d9' }} />

<Row>
  <Col xs={24} sm={24} md={24} lg={24}>
    <ReturnMapServiceParametersSearch callApi={callApi} handleSearchCriteria={handleSearchCriteria} />
  </Col>
</Row>

<div style={{ border: '1px solid #d9d9d9', background: "white", padding: '7px', borderRadius: '4px', marginTop: '15px', display: 'flex', justifyContent: 'space-between', boxShadow: '0 2px 8px rgba(0, 0, 0, 0.1)' }}>
  <h5 style={{ marginTop: "10px", textAlign: "center" }}>Hata Kodları Parametreleri</h5>
  <SecureButton permission="showModalCreate" type="primary" onClick={showModalCreate}>Ekle</SecureButton>
</div>

<Row>
  <Col xs={24} sm={24} md={24} lg={24}>
    <ReturnMapServiceParametersTable definitionList={definitionList} />
  </Col>
</Row>
