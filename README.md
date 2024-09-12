  {
        subTableActive &&
        <>
          <Row>
            <Col xs={24} sm={24} md={24} lg={24}>
              <ReturnMapServiceParametersSearch callApi={callApi} handleSearchCriteria={handleSearchCriteria} />
            </Col>
          </Row>

          <div style={{ border: '1px solid #d9d9d9', background: "white", padding: '7px', borderRadius: '4px', marginTop: '15px', display: 'flex', justifyContent: 'space-between' }}>
            <h5 style={{ marginTop: "10px", textAlign: "center" }}>{ReturnMapFormLocale.returnMapHead.label}</h5>
            <SecureButton permission="showModalCreate" type="primary" onClick={showModalCreate}>{ReturnMapFormLocale.createButton}</SecureButton>
          </div>

          <Row >
            <Col xs={24} sm={24} md={24} lg={24}>
              <ReturnMapServiceParametersTable definitionList={definitionListIsActiveTrue} />
            </Col>
          </Row>
        </>
      }
