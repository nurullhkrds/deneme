import React, { useState, useEffect } from 'react';
import { useDispatch } from 'react-redux';
import { fetchReturnMapDefinitionByReturnMapCode } from 'your-action-file'; // İlgili aksiyonu import edin
import { Select, Form } from 'your-ui-library';
const { Option } = Select;

const YourComponent = ({ returnMapOneData }) => {
  const [definitionList, setDefinitionList] = useState([]);
  const [definition, setDefinition] = useState(null);
  const dispatch = useDispatch();

  useEffect(() => {
    // Veriyi çekmek için dispatch kullanın
    const fetchData = async () => {
      const response = await dispatch(fetchReturnMapDefinitionByReturnMapCode(returnMapOneData?.returnMapCode));
      setDefinitionList(response); // Aldığınız listeyi state'e kaydedin
      setDefinition(response.find(item => item.returnMapCode === returnMapOneData?.returnMapCode)); // Varsayılan seçili değeri ayarlayın
    };
    fetchData();
  }, [dispatch, returnMapOneData]);

  const handleChangeSelectDefinition = (value) => {
    const selectedDefinition = definitionList.find(item => item.id === value);
    setDefinition(selectedDefinition);
    // İhtiyacınıza göre başka işlemler yapabilirsiniz
  };

  return (
    <Form.Item label="ReturnMap Seç">
      <Select value={definition?.id} onChange={handleChangeSelectDefinition}>
        {definitionList.map(item => (
          <Option key={item.id} value={item.id}>
            {item.returnMapCode}
          </Option>
        ))}
      </Select>
    </Form.Item>
  );
};

export default YourComponent;
