import { Select, Form } from 'antd';
import { useDispatch, useSelector } from 'react-redux';
import { useState, useRef } from 'react';

const ReturnMapDefinitionServiceParametersSearch = ({ callApi, dataList }) => {
  const dispatch = useDispatch();
  const ref = useRef(null);
  const [returnMapCode, setReturnMapCode] = useState('');
  const definitionList = useSelector((state) => state.returnMap.returnMapDefinitionList);

  const handleDefinitionSearch = () => {
    if (returnMapCode) {
      dispatch(fetchReturnMapDefinitionByReturnMapCode(dispatch, callApi, returnMapCode));
    }
  };

  const handleDefinitionReset = () => {
    setReturnMapCode('');
    if (ref.current) {
      ref.current.setFieldsValue('');
    }
    dispatch(setReturnMapDefinitionData(null));
  };

  const handleSelectChange = (value) => {
    setReturnMapCode(value); // Seçilen değeri state'e set et
  };

  return (
    <div>
      <Form ref={ref}>
        <Form.Item colSpan={{ xs: 24, sm: 24, md: 6, lg: 6 }} label="Dönüş Kodu">
          <Select
            value={returnMapCode}
            onChange={handleSelectChange}
            placeholder="Bir dönüş kodu seçin"
            style={{ width: '100%' }}
          >
            {dataList.map((item) => (
              <Select.Option key={item.id} value={item.returnMapCode}>
                {item.name} {/* item.name ya da gösterilecek veri */}
              </Select.Option>
            ))}
          </Select>
        </Form.Item>

        <Form.Item colSpan={{ xs: 24, sm: 24, md: 6, lg: 6 }}>
          <SecureButton type="secondary" permission="handleDefinitionReset" onClick={handleDefinitionReset}>
            Temizle
          </SecureButton>
          <SecureButton type="primary" permission="handleDefinitionSearch" onClick={handleDefinitionSearch}>
            Listele
          </SecureButton>
        </Form.Item>
      </Form>
    </div>
  );
};

export default ReturnMapDefinitionServiceParametersSearch;
