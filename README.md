// apiUtils.js
import { sendSearchReturnMapRequest } from '../components/api/returnMapServiceParameter/ReturnMapServiceParametersApi';
import { setReturnMapList, setSpinning, setError } from '../redux/slices/returnMapServiceParameter/returnMapServiceParameterSlice';

export const fetchInstitutionsData = (callApi, searchCriteria) => async (dispatch) => {
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


import React, { Fragment, useState } from 'react';
import { useDispatch } from 'react-redux';
import { Button, Form, TextInput } from 'ykb-ui';
import { fetchInstitutionsData } from '../../utils/apiUtils'; // Yardımcı fonksiyonu import et

const ReturnMapServiceParametersSearch = ({ callApi }) => {
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
    dispatch(fetchInstitutionsData(callApi, searchCriteria));
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
        <Button
          type="secondary"
          onClick={() => {
            setSearchCriteria({
              returnMapCode: '',
              bankReturnCode: '',
              institutionReturnCode: '',
            });
            dispatch(fetchInstitutionsData(callApi, {}));
          }}
        >
          Reset
        </Button>
      </Form>
    </Fragment>
  );
};

export default ReturnMapServiceParametersSearch;
