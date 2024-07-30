<>

      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersSearch callApi={callApi} handleSearchCriteria={handleSearchCriteria} />
        </Col>
      </Row>

      <Button type="primary" onClick={showModal}>Ekle</Button>

      <Row style={{ marginTop: '15px ' }}>


        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersTable />
        </Col>
      </Row>

      <Row style={{ marginTop: "25px" }}>
        <Col xs={24} sm={24} md={24} lg={24}>
          {
            selectedIds.length > 0 && (
              <Button onClick={showModalCopy}>Kopyala</Button>
            )
          }
          {
            selectedIds.length > 0 && (
              <Button type="danger" onClick={handleClickPromptCustomButtonText}>
                {`(${selectedIds?.length}) Sil`}
              </Button>
            )
          }


        </Col>
      </Row>

      

    </>
