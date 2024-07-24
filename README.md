// src/utils/fetchInstitutionsData.js
import { setSpinning, setReturnMapList, setError } from '../redux/slices/returnMapServiceParameter/returnMapServiceParameterSlice';
import { sendSearchReturnMapRequest } from '../components/api/returnMapServiceParameter/ReturnMapServiceParametersApi';

export const fetchInstitutionsData = async (dispatch, callApi, searchCriteria = {}) => {
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
