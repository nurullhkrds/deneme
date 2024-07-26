import React, { useEffect, useMemo, useState } from 'react';
import { Table, Button, Notification, Modal, Form, TextInput, Select, Checkbox } from 'ykb-ui';
import { useSelector, useDispatch } from 'react-redux';
import { setSelectedReturnMapList } from '../../redux/slices/returnMapServiceParameter/returnMapServiceParameterSlice';
import { useShellCommunicator } from 'ykb-shell';
import { fetchInstitutionsData, fetchReturnMapById } from '../../service/ApiService';
import { sendUpdateReturnMapRequest } from '../../components/api/returnMapServiceParameter/ReturnMapServiceParametersApi';
const Option = Select.Option;

const ReturnMapServiceParametersTable = () => {
  const returnMapData = useSelector((state) => state.returnMap.list);
  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator(); // Adjust this if needed

  const dataWithKeys = useMemo(
    () => returnMapData.map((item, index) => ({ ...item, key: index + 1 })),
    [returnMapData]
  );




  const rowSelection = {
    onChange: (selectedRowKeys, selectedRows) => {
      console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows);
      const selectedIds = selectedRows.map(row => row.id);
      dispatch(setSelectedReturnMapList(selectedIds));
    },
    getCheckboxProps: record => ({
      disabled: record.name === 'Disabled User',
      name: record.name,
    }),
  };




  const columns = [
    {
      title: 'Return Map Kodu',
      dataIndex: 'returnMapCode',
      key: 'returnMapCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Kurum Kodu',
      dataIndex: 'institutionReturnCode',
      key: 'institutionReturnCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Kurum Metni',
      dataIndex: 'institutionReturnText',
      key: 'institutionReturnText',
      width: 200,
      resizable: true,
    },
    {
      title: 'Banka Kodu',
      dataIndex: 'bankReturnCode',
      key: 'bankReturnCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Banka Metni',
      dataIndex: 'bankReturnText',
      key: 'bankReturnText',
      width: 200,
      resizable: true,
    },
    {
      title: 'Dönüş Tipi',
      dataIndex: 'returnType',
      key: 'returnType',
      width: 200,
      resizable: true,
    },
    {
      title: 'Geri Dönüş',
      dataIndex: 'isReversible',
      key: 'isReversible',
      width: 200,
      render: value => (value ? 'Evet' : 'Hayır'),
      resizable: true,
    },
    {
      title: 'Actions',
      key: 'actions',
      width: 100,
      render: (text, record) => (
        <Button onClick={() => handleEdit(record)}>Düzenle</Button>
      ),
    },
  ];

  return (

    <>

      <Table rowSelection={rowSelection} columns={columns} data={dataWithKeys} />


export default ReturnMapServiceParametersTable;












import React, { useState } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { Row, Col, Button, Modal, Form, Select, Notification } from 'ykb-ui';

import { useShellCommunicator } from 'ykb-shell';
import { sendCopyReturnMapRequest, sendcreateReturnMapRequest } from '../../components/api/returnMapServiceParameter/ReturnMapServiceParametersApi';
import ReturnMapServiceParametersTable from './ReturnMapServiceParametersTable';
import ReturnMapServiceParametersSearch from './ReturnMapServiceParametersSearch';
import { fetchInstitutionsData } from '../../service/ApiService';
const Option = Select.Option;

function ReturnMapServiceParameters() {
  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator();

  //modal 2 
  const [modalVisible2, setModalVisible2] = useState(false);
  const [returnMapCode2, setReturnMapCode2] = useState("");
  const selectedIds = useSelector((state) => state.returnMap.selectedList);



  const copyRequest = {

    ids: selectedIds,
    returnMapCode: returnMapCode2,


  }
 

  //modal2 
  const showModal2 = () => {
    setModalVisible2(true);
  };

  const handleOk2 = () => {

    setModalVisible2(false);

    sendCopyReturnMapRequest(callApi, copyRequest)
      .then(() => {
        dispatch(fetchInstitutionsData(dispatch, callApi, returnMapCode2));
        Notification.success('Kopyalama Başarılı', 3);
      })
      .catch(error => {
        console.error('Error creating return map:', error);
        Notification.error('Hatalı Kopyalama', 3);
      });

    setbankReturnCode2("")
    setIsReversible2(false)
    setReturnMapCode2("")
    setbankReturnText2("")
    setinstitutionReturnCode2("")
    setinstitutionReturnText2("")
    setreturnType2("")

  };

  const handleCancel2 = () => {
    setModalVisible2(false);
  };


  const handleReturnMapCode2 = (e) => {
    setReturnMapCode2(e.target.value)
  }







  return (
    <>
      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersSearch callApi={callApi} />
        </Col>
      </Row>
      <Row style={{ marginTop: '15px ' }}>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersTable />
        </Col>
      </Row>
      <Row style={{ marginTop: "25px" }}>
        <Col xs={24} sm={24} md={24} lg={24}>
          <Button onClick={showModal}>Ekle</Button>
          {
            selectedIds.length > 0 && (
              <Button onClick={showModal2}>Kopyala</Button>
            )
          }

        </Col>
      </Row>



      {/* ikinci modal */}
      <section>
        <Modal
          title="ReturnMap"
          visible={modalVisible2}
          onClose={handleCancel2}
          footer={[
            <Button key="cancel" onClick={handleCancel2}>
              Cancel
            </Button>,
            <Button key="ok" type="primary" onClick={handleOk2}>
              Save
            </Button>,
          ]}
        >

          <div>
            <Form >
              <Form.Item label="ReturnMap Kodu" >
                <input
                  name="returnMapCode2"
                  value={returnMapCode2}
                  onChange={handleReturnMapCode2}
                  style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
                />
              </Form.Item>
            </Form>
          </div>

        </Modal>

      </section>

    </>
  );
}

export default ReturnMapServiceParameters;






    </>
  );
};

