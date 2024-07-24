import React, { useEffect, useState } from 'react';
import { Table, TableHeader, Tooltip,Button,Row,Col } from 'ykb-ui';
import { useSelector, useDispatch } from 'react-redux';

const columns = [
  {
    title: 'Return Map Code',
    dataIndex: 'returnMapCode',
  },
  {
    title: 'Institution Return Code',
    dataIndex: 'institutionReturnCode',
  },
  {
    title: 'Institution Return Text',
    dataIndex: 'institutionReturnText',
  },
  {
    title: 'Bank Return Code',
    dataIndex: 'bankReturnCode',
  },
  {
    title: 'Bank Return Text',
    dataIndex: 'bankReturnText',
  },
  {
    title: 'Return Type',
    dataIndex: 'returnType',
  },
  {
    title: 'Is Reversible',
    dataIndex: 'isReversible',
    render: value => (value ? 'Yes' : 'No'),
  },
];

function ReturnMapServiceParametersTable() {
  const [selectedRowKeys, setSelectedRowKeys] = useState([]);
  const returnMapData = useSelector((state) => state.returnMap.list);
 

  const onChange = (selectedRowKeys, selectedRows) => {
    console.log(
      'selectedRowKeys:',
      selectedRowKeys,
      'selectedRows: ',
      selectedRows
    );

    setSelectedRowKeys(selectedRowKeys);
  };

  return (
    <Table
      rowSelection={{
        getCheckboxProps: record => ({
          disabled: record.name === 'a5',
        }),
        selectedRowKeys: selectedRowKeys,
        onChange: onChange,
      }}
      data={returnMapData}
      columns={columns}
      pagination={{ showTotal: false }}
      title={
        <TableHeader
          extra={<TableHeader.GhostButton icon="round-plus" title="Create" />}
        />
      }
      footer={() => (
        <React.Fragment>
          <Row>
            <Col xs={24}>
              <p
                style={{
                  margin: '0',
                  height: '30px',
                  lineHeight: '30px',
                }}>
                Lorem ipsum dolor sit amet
                <Button size="small" className="right-floated">
                  Button 1
                </Button>
              </p>
            </Col>
          </Row>
        </React.Fragment>
      )}
      emptyText={() => (
        <React.Fragment>
          Yeni eklemek için
          <Button link="#">tıklayınız.</Button>
        </React.Fragment>
      )}
      onRow={(record, index) => ({
        onClick: () => {
          console.log('onRowClick', record, index);
        },
      })}
    />
  );
}

export default ReturnMapServiceParametersTable; table.........


// ReturnMapServiceParametersSearch.js
import React, { Fragment, useState } from 'react';
import { useDispatch } from 'react-redux';
import { Button, Form, TextInput } from 'ykb-ui';

const ReturnMapServiceParametersSearch = () => {
  const dispatch = useDispatch();
  const [searchCriteria, setSearchCriteria] = useState({
    returnMapCode: '',
    bankReturnCode: '',
    institutionReturnCode: '',
  });

  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setSearchCriteria((prevCriteria) => ({
      ...prevCriteria,
      [name]: value,
    }));
  };

  const handleSearch = () => {
    //dispatch(fetchReturnMapList(searchCriteria));
  };

  return (
    <Fragment>
      <Form>
        <Form.Item label="ReturnMap Kodu">
          <TextInput
            name="returnMapCode"
            value={searchCriteria.returnMapCode}
            onChange={handleInputChange}
            allowClear
          />
        </Form.Item>
        <Form.Item label="Kurum Kodu">
          <TextInput
            name="institutionReturnCode"
            value={searchCriteria.institutionReturnCode}
            onChange={handleInputChange}
            allowClear
          />
        </Form.Item>
        <Form.Item label="Banka Kodu">
          <TextInput
            name="bankReturnCode"
            value={searchCriteria.bankReturnCode}
            onChange={handleInputChange}
            allowClear
          />
        </Form.Item>
        <Button type="primary" onClick={handleSearch}>Search</Button>
      </Form>
    </Fragment>
  );
};

export default ReturnMapServiceParametersSearch;



import React, { useEffect, useRef, useState } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { Row, Col, Input, Spin, Table, Button } from 'ykb-ui';

import { useShellCommunicator } from 'ykb-shell';
import { sendSearchReturnMapRequest } from '../../components/api/returnMapServiceParameter/ReturnMapServiceParametersApi';
import { setError, setReturnMapList, setSpinning } from '../../redux/slices/returnMapServiceParameter/returnMapServiceParameterSlice';
import ReturnMapServiceParametersTable from './ReturnMapServiceParametersTable';
import ReturnMapServiceParametersSearch from './ReturnMapServiceParametersSearch';

function ReturnMapServiceParameters() {
  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator();

  const fetchInstitutionsData = async (searchCriteria = {}) => {
    dispatch(setSpinning(true));
    try {
      let response = await sendSearchReturnMapRequest(callApi, searchCriteria);
      dispatch(setReturnMapList(response.data));
    } catch (error) {
      dispatch(setError(error));
      console.error("fetchInstitutionsData", error);
    } finally {
      dispatch(setSpinning(false));
    }
  };

  useEffect(() => {
    fetchInstitutionsData();
  }, []);

  return (
    <>
      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersSearch fetchInstitutionsData={fetchInstitutionsData} />
        </Col>
      </Row>
      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersTable />
        </Col>
      </Row>
      <Row style={{ marginTop: "25px" }}>
        <Col xs={24} sm={24} md={24} lg={24}>
          <Button>Ekle</Button>
          <Button>Güncelle</Button>
          <Button>Kopyala</Button>
        </Col>
      </Row>
    </>
  );
}

export default ReturnMapServiceParameters;


import { createSlice } from '@reduxjs/toolkit';

const returnMapSlice = createSlice({
  name: 'returnMap',
  initialState: {
    list: [],
    spinning: false,
    error: null,
  },
  reducers: {
    setReturnMapList: (state, action) => {
      state.list = action.payload;
    },
    setSpinning: (state, action) => {
      state.spinning = action.payload;
    },
    setError: (state, action) => {
      state.error = action.payload;
    },
  },
});

export const { setReturnMapList, setSpinning, setError } = returnMapSlice.actions;
export default returnMapSlice.reducer;


merhaba bu

