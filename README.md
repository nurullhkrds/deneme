import React, { useEffect, useState } from 'react';
import { Button, Form, Select, CheckboxGroup, Checkbox, TextInput, DatePicker } from 'ykb-ui';
import { formFieldsConfig } from './formFieldsConfig';

const FormItem = Form.Item;

function BillLogMonitoring() {
  const [formData, setFormData] = useState({});
  const [formOptions, setFormOptions] = useState({});

  // API'den seçenekleri dinamik olarak yükleme
  useEffect(() => {
    async function fetchOptions() {
      const optionsData = {};
      await Promise.all(
        Object.entries(formFieldsConfig).map(async ([key, { optionsEndPoint }]) => {
          if (optionsEndPoint) {
            const response = await fetch(optionsEndPoint);
            optionsData[key] = await response.json();
          }
        })
      );
      setFormOptions(optionsData);
    }

    fetchOptions();
  }, []);

  // Form elemanlarındaki değişiklikleri işleme
  const handleChange = (name, value) => {
    setFormData({ ...formData, [name]: value });
  };

  // Formu gönderme
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form Data:', formData);
  };

  // Formu sıfırlama
  const handleReset = () => {
    setFormData({});
  };

  return (
    <Form onSubmit={handleSubmit}>
      {Object.entries(formFieldsConfig).map(([key, config]) => {
        const Component = config.type === 'select' ? Select : config.type === 'textInput' ? TextInput : config.type === 'datePicker' ? DatePicker : CheckboxGroup;
        const options = formOptions[key] || config.options;

        return (
          <FormItem label={config.label} key={key} required={config.required}>
            {Component === CheckboxGroup ? (
              <CheckboxGroup name={key} onChange={(e) => handleChange(key, e.target.value)}>
                {options.map(option => (
                  <Checkbox key={option.value} value={option.value}>{option.label}</Checkbox>
                ))}
              </CheckboxGroup>
            ) : (
              <Component name={key} onChange={(value) => handleChange(key, value)}>
                {options && Component !== CheckboxGroup && options.map(option => (
                  <Component.Option key={option.value} value={option.value}>{option.label}</Component.Option>
                ))}
              </Component>
            )}
          </FormItem>
        );
      })}
      <FormItem>
        <Button onClick={handleReset} type="secondary">Reset</Button>
        <Button type="submit" type="primary">Submit</Button>
      </FormItem>
    </Form>
  );
}

export default BillLogMonitoring;
