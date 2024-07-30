<>
  <Row>
    <Col xs={24} sm={24} md={24} lg={24}>
      <ReturnMapServiceParametersSearch callApi={callApi} handleSearchCriteria={handleSearchCriteria} />
    </Col>
  </Row>

  <div style={{ display: 'flex', justifyContent: 'flex-end', marginTop: '15px' }}>
    <Button type="primary" onClick={showModal}>Ekle</Button>
  </div>

  <Row style={{ marginTop: '15px' }}>
    <Col xs={24} sm={24} md={24} lg={24}>
      <ReturnMapServiceParametersTable />
    </Col>
  </Row>

  <div style={{ display: 'flex', justifyContent: 'flex-end', marginTop: "25px" }}>
    {selectedIds.length > 0 && (
      <>
        <Button onClick={showModalCopy} style={{ marginRight: '10px' }}>Kopyala</Button>
        <Button type="danger" onClick={handleClickPromptCustomButtonText}>
          {`(${selectedIds?.length}) Sil`}
        </Button>
      </>
    )}
  </div>
</>
