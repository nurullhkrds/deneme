import React, { useEffect, useState, useRef } from 'react';
import { Button, Form, Select, CheckboxGroup, TextInput, DatePicker, Checkbox } from 'ykb-ui';

const FormItem = Form.Item;

function BillLogMonitoring() {
  const formRef = useRef(null);
  const [formOptions, setFormOptions] = useState({});
  const [formData, setFormData] = useState({});
  const [isMicro, setIsMicro] = useState(false);

  useEffect(() => {
    async function fetchOptions() {
      const optionsData = {};
      for (const field in formFieldsConfig) {
        if (formFieldsConfig[field].optionsEndPoint) {
          const response = await fetch(formFieldsConfig[field].optionsEndPoint);
          const data = await response.json();
          optionsData[field] = data.map(option => ({ label: option.name, value: option.id }));
        }
      }
      setFormOptions(optionsData);
    }

    fetchOptions();
  }, []);

  const handleChange = (name, value) => {
    if (name === 'isMicro') {
      setIsMicro(value);
    }
    setFormData({ ...formData, [name]: value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form Data:', formData);
  };

  const handleReset = () => {
    setFormData({});
    setIsMicro(false);
  };

  return (
    <Form ref={formRef} onSubmit={handleSubmit}>
      {Object.entries(formFieldsConfig).map(([key, config]) => {
        const options = formOptions[key] || config.options;
        const Component = config.type === 'select' ? Select : config.type === 'textInput' ? TextInput : config.type === 'datePicker' ? DatePicker : CheckboxGroup;

        return (
          <FormItem label={config.label} key={key} required={config.required}>
            {Component === CheckboxGroup ? (
              <CheckboxGroup name={key} onChange={(value) => handleChange(key, value)}>
                {options.map((option) => (
                  <Checkbox key={option.value} value={option.value}>{option.label}</Checkbox>
                ))}
              </CheckboxGroup>
            ) : (
              <Component name={key} options={options} onChange={(value) => handleChange(key, value)}>
                {options && Component !== CheckboxGroup && options.map(option => (
                  <Component.Option key={option.value} value={option.value}>{option.label}</Component.Option>
                ))}
              </Component>
            )}
          </FormItem>
        );
      })}
      <Button onClick={handleReset} type="secondary">Reset</Button>
      <Button type="submit" type="primary">Submit</Button>
    </Form>
  );
}

export default BillLogMonitoring;
