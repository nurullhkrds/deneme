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
  const returnMapOneData = useSelector((state) => state.returnMap.returnMapData);
  const formRef = React.useRef(null);
  useEffect(() => {
    if (formRef.current) {
      formRef.current.setFieldsValue(returnMapOneData);
      setIsReversible(returnMapOneData.isReversible)
      setreturnType(returnMapOneData.returnType)
    }
  }, [returnMapOneData]);
  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator(); // Adjust this if needed
  const [modalVisible, setModalVisible] = useState(false);
  const [returnMapCode, setReturnMapCode] = useState(returnMapOneData?.returnMapCode);
  const [institutionReturnCode, setinstitutionReturnCode] = useState(returnMapOneData?.institutionReturnCode);
  const [institutionReturnText, setinstitutionReturnText] = useState(returnMapOneData?.institutionReturnText);
  const [bankReturnCode, setbankReturnCode] = useState(returnMapOneData?.bankReturnCode);
  const [bankReturnText, setbankReturnText] = useState(returnMapOneData?.bankReturnText);
  const [returnType, setreturnType] = useState(returnMapOneData?.returnType);
  const [isReversible, setIsReversible] = useState(returnMapOneData?.isReversible);
  const [updatedId, setUpdatedId] = useState();
  const dataWithKeys = useMemo(
    () => returnMapData.map((item, index) => ({ ...item, key: index + 1 })),
    [returnMapData]
  );


  const updateData = {
    id: updatedId,
    returnMapCode: returnMapCode,
    institutionReturnCode: institutionReturnCode,
    institutionReturnText: institutionReturnText,
    bankReturnCode: bankReturnCode,
    bankReturnText: bankReturnText,
    returnType: returnType,
    isReversible: isReversible

  }

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

  const handleEdit = async (record) => {
    showModal()
    await dispatch(fetchReturnMapById(dispatch, callApi, record.id))
    setUpdatedId(record.id)
  };

  const showModal = () => {
    setModalVisible(true);
  };

  const handleOk = (record) => {
    setModalVisible(false);
    // sendUpdateReturnMapRequest(callApi, updateData)
    //   .then(() => {
    //     dispatch(fetchInstitutionsData(dispatch, callApi, {}));
    //     Notification.success('Güncelleme Başarılı', 3);
    //   })
    //   .catch(error => {
    //     console.error('Error creating return map:', error);
    //     Notification.error('Hatalı güncelleme', 3);
    //   });
    console.log(updateData)
  };

  const handleCancel = () => {
    setModalVisible(false);
  };

  console.log(bankReturnText)

  const handleReturnMapCode = (e) => {
    setReturnMapCode(e.target.value)
  }

  const handleInstitutionReturnCode = (e) => {
    setinstitutionReturnCode(e.target.value)
  }

  const handleInstitutionReturnText = (e) => {
    setinstitutionReturnText(e.target.value)
    console.log("1-", e.target.value)
    console.log("2-", institutionReturnText)
  }


  const handleBankReturnCode = (e) => {
    setbankReturnCode(e.target.value)
  }

  const handleBankReturnText = (e) => {
    setbankReturnText(e.target.value)
  }

  const handleSelectReturnType = value => {
    console.log('selected', value);
    setreturnType(value);
  };
  const handleSelectReversible = value => {

    setIsReversible(!isReversible)
  };

  console.log(isReversible)


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
        <Button onClick={() => handleEdit(record)}>Edit</Button>
      ),
    },
  ];

  return (

    <>

      <Table rowSelection={rowSelection} columns={columns} data={dataWithKeys} />



      <section>
        <Modal
          title="ReturnMap"
          visible={modalVisible}
          onClose={handleCancel}
          footer={[
            <Button key="cancel" onClick={handleCancel}>
              Cancel
            </Button>,
            <Button key="ok" type="primary" onClick={handleOk}>
              Save
            </Button>,
          ]}
        >

          <div>
            <Form ref={formRef}>
              <Form.Item label="ReturnMap Kodu">
                <TextInput
                  name="returnMapCode"
                  value={returnMapCode}
                  onChange={handleReturnMapCode}
                />
              </Form.Item>

              <Form.Item label="Kurum Kodu">
                <TextInput
                  name="institutionReturnCode"
                  value={institutionReturnCode}
                  onChange={handleInstitutionReturnCode}
                />
              </Form.Item>
              <Form.Item label="Kurum Metni">
                <TextInput
                  name="institutionReturnText"
                  value={institutionReturnText}
                  onChange={handleInstitutionReturnText}
                />
              </Form.Item>
              <Form.Item label="Banka Kodu">
                <TextInput
                  name="bankReturnCode"
                  value={bankReturnCode}
                  onChange={handleBankReturnCode}
                />
              </Form.Item>
              <Form.Item label="Banka Metni">
                <TextInput
                  name="bankReturnText"
                  value={bankReturnText}
                  onChange={handleBankReturnText}
                />
              </Form.Item>

              <Form.Item label="Geri Dönüş Tipi">
                <Select defaultValue={returnType}

                  value={returnType}
                  onChange={handleSelectReturnType}>
                  <Option value="SUCCESS">
                    Başarılı
                  </Option>
                  <Option value="ERROR" >
                    Başarısız
                  </Option>
                </Select>
              </Form.Item>
              <Form.Item >
                <Checkbox textLabel="Geri Dönüşüm" checked={isReversible} onChange={handleSelectReversible} />
              </Form.Item>
            </Form>
          </div>
        </Modal>

      </section>

    </>
  );
};

export default ReturnMapServiceParametersTable;
