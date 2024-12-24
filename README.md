import React, { useEffect, useState } from 'react';
import { Button, Form, Select, CheckboxGroup, TextInput, DatePicker } from 'ykb-ui';
import { formFieldsConfig } from './formConfig';
const FormItem = Form.Item;

function BillLogMonitoring() {
  const [formOptions, setFormOptions] = useState({});
  const [formData, setFormData] = useState({});
  const formRef = React.useRef(null);

  // Form yapılandırmasına göre API'den seçenekleri çekme
  useEffect(() => {
    async function fetchOptions() {
      const fetchedOptions = {};
      for (const field in formFieldsConfig) {
        const config = formFieldsConfig[field];
        if (config.optionsEndPoint) {
          const response = await fetch(config.optionsEndPoint);
          fetchedOptions[field] = await response.json();
        }
      }
      setFormOptions(fetchedOptions);
    }

    fetchOptions();
  }, []);

  const handleSubmit = async (e) => {
    e.preventDefault();
    const values = formRef.current.getValues();
    // Submit logic here
    console.log(values);
  };

  return (
    <Form ref={formRef} onSubmit={handleSubmit}>
      {Object.entries(formFieldsConfig).map(([key, config]) => {
        const Component = config.type === 'select' ? Select : config.type === 'checkboxGroup' ? CheckboxGroup : TextInput;
        const options = formOptions[key] || config.options;
        
        return (
          <FormItem label={config.label} key={key} required={config.required}>
            <Component
              name={key}
              options={options}
              onChange={value => config.requiredDependent && setFormData({...formData, [config.requiredDependent]: value === 'true'})}
            >
              {options && options.map(option => <Component.Option key={option.value} value={option.value}>{option.label}</Component.Option>)}
            </Component>
          </FormItem>
        );
      })}
      <Button type="submit">Submit</Button>
    </Form>
  );
}

export default BillLogMonitoring;
