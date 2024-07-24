import React, { useState, useEffect } from 'react';
import { Modal, Spin, Button, Form, Input, Select } from 'ykb-ui';
import PropTypes from 'prop-types';
import { useDispatch, useSelector } from 'react-redux';
import { sendSaveInstServiceParameterRequest } from './api'; // API isteği fonksiyonunun yolu

const { Option } = Select;

const CreateOrUpdateReturnMapModal = ({ visible, onClose, onOk, initialValues = {} }) => {
  const dispatch = useDispatch();
  const [formValues, setFormValues] = useState({
    returnMapCode: '',
    institutionReturnCode: '',
    institutionReturnText: '',
    bankReturnCode: '',
    bankReturnText: '',
    returnType: 'ERROR',
    isReversible: true,
    ...initialValues, // initialValues ile doldur
  });
  const [spinning, setSpinning] = useState(false);

  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setFormValues((prev) => ({ ...prev, [name]: value }));
  };

  const handleSelectChange = (value) => {
    setFormValues((prev) => ({ ...prev, returnType: value }));
  };

  const handleSubmit = async () => {
    setSpinning(true);
    try {
      await sendSaveInstServiceParameterRequest(dispatch, formValues);
      onOk(); // Modal kapatılır ve liste güncellenir
    } catch (error) {
      console.error('Error saving data:', error);
    } finally {
      setSpinning(false);
    }
  };

  useEffect(() => {
    if (visible && initialValues) {
      setFormValues((prev) => ({ ...prev, ...initialValues }));
    }
  }, [visible, initialValues]);

  return (
    <Modal
      title="Create or Update Return Map"
      visible={visible}
      onClose={onClose}
      size="large"
    >
      <Spin spinning={spinning}>
        <Form layout="vertical">
          <Form.Item label="Return Map Code">
            <Input
              name="returnMapCode"
              value={formValues.returnMapCode}
              onChange={handleInputChange}
            />
          </Form.Item>
          <Form.Item label="Institution Return Code">
            <Input
              name="institutionReturnCode"
              value={formValues.institutionReturnCode}
              onChange={handleInputChange}
            />
          </Form.Item>
          <Form.Item label="Institution Return Text">
            <Input
              name="institutionReturnText"
              value={formValues.institutionReturnText}
              onChange={handleInputChange}
            />
          </Form.Item>
          <Form.Item label="Bank Return Code">
            <Input
              name="bankReturnCode"
              value={formValues.bankReturnCode}
              onChange={handleInputChange}
            />
          </Form.Item>
          <Form.Item label="Bank Return Text">
            <Input
              name="bankReturnText"
              value={formValues.bankReturnText}
              onChange={handleInputChange}
            />
          </Form.Item>
          <Form.Item label="Return Type">
            <Select
              value={formValues.returnType}
              onChange={handleSelectChange}
            >
              <Option value="ERROR">ERROR</Option>
              <Option value="SUCCESS">SUCCESS</Option>
            </Select>
          </Form.Item>
          <Form.Item label="Is Reversible">
            <Input
              type="checkbox"
              checked={formValues.isReversible}
              onChange={(e) => setFormValues((prev) => ({ ...prev, isReversible: e.target.checked }))}
            />
          </Form.Item>
          <Form.Item>
            <Button type="primary" onClick={handleSubmit}>Save</Button>
            <Button onClick={onClose}>Cancel</Button>
          </Form.Item>
        </Form>
      </Spin>
    </Modal>
  );
};

CreateOrUpdateReturnMapModal.propTypes = {
  visible: PropTypes.bool.isRequired,
  onClose: PropTypes.func.isRequired,
  onOk: PropTypes.func.isRequired,
  initialValues: PropTypes.object,
};

export default CreateOrUpdateReturnMapModal;











export const sendSaveInstServiceParameterRequest = async (dispatch, instServiceParameter) => {
  const endpoint = "returnMaps/createReturnMap";
  try {
    return await callApi({
      method: "POST",
      endpoint: endpoint,
      body: instServiceParameter,
      notifyErrors: true, // Bu şekilde hata mesajlarını kullanıcıya gösterebilirsiniz
    });
  } catch (ex) {
    console.error('API error:', ex);
    throw ex; // Hataları üst bileşene fırlat
  }
};



import React, { useEffect, useState } from 'react';
import { useDispatch } from 'react-redux';
import { Button, Col, Row } from 'ykb-ui';
import ReturnMapServiceParametersSearch from './ReturnMapServiceParametersSearch';
import ReturnMapServiceParametersTable from './ReturnMapServiceParametersTable';
import InstServiceParameterDetailModal from './InstServiceParameterDetailModal'; // Import the modal component
import { fetchInstitutionsData } from './your-actions-file'; // Adjust the import path

function ReturnMapServiceParameters() {
  const dispatch = useDispatch();
  const { callApi } = useShellCommunicator(); // Adjust this if needed

  const [isModalVisible, setModalVisible] = useState(false);
  const [modalMode, setModalMode] = useState(''); // 'add' or 'update'

  const openModal = (mode) => {
    setModalMode(mode);
    setModalVisible(true);
  };

  const closeModal = () => {
    setModalVisible(false);
  };

  const handleFormSubmit = async (formData) => {
    try {
      const endpoint = 'returnMaps/createReturnMap'; // Adjust endpoint if necessary
      await callApi({
        method: 'POST',
        endpoint,
        body: formData,
      });
      // Refresh data after successful operation
      dispatch(fetchInstitutionsData(dispatch, callApi, {}));
      closeModal();
    } catch (error) {
      console.error('Error saving data:', error);
    }
  };

  useEffect(() => {
    dispatch(fetchInstitutionsData(dispatch, callApi, {}));
  }, [dispatch, callApi]);

  return (
    <>
      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersSearch callApi={callApi} />
        </Col>
      </Row>
      <Row>
        <Col xs={24} sm={24} md={24} lg={24}>
          <ReturnMapServiceParametersTable />
        </Col>
      </Row>
      <Row style={{ marginTop: "25px" }}>
        <Col xs={24} sm={24} md={24} lg={24}>
          <Button onClick={() => openModal('add')}>Ekle</Button>
          <Button onClick={() => openModal('update')}>Güncelle</Button>
          <Button>Kopyala</Button>
        </Col>
      </Row>
      {isModalVisible && (
        <InstServiceParameterDetailModal
          handleOk={handleFormSubmit}
          handleClose={closeModal}
          mode={modalMode} // Pass the mode to the modal if needed
        />
      )}
    </>
  );
}

export default ReturnMapServiceParameters;
