import React, { useEffect, useState } from 'react';
import { Button, Form, Select, CheckboxGroup, TextInput, DatePicker } from 'antd'; // UI library; burada antd varsayılmıştır
import axios from 'axios';
import { formFieldsConfig } from './formConfig'; // yapılandırma dosyasını import ediyoruz

const FormItem = Form.Item;

const BillLogMonitoring = () => {
  const [formData, setFormData] = useState({});
  const [formOptions, setFormOptions] = useState({});

  // Dinamik veri yüklemek için API çağrıları
  useEffect(() => {
    const fetchOptions = async () => {
      const promises = Object.entries(formFieldsConfig).map(([key, value]) => {
        if (value.optionsEndPoint) {
          return axios.get(value.optionsEndPoint).then(res => ({
            key,
            options: res.data.map(option => ({ label: option.name, value: option.id })) // API'nin dönüş formatına bağlı olarak ayarlayın
          }));
        }
        return Promise.resolve(null);
      });

      const results = await Promise.all(promises);
      const newOptions = results.reduce((acc, curr) => {
        if (curr) acc[curr.key] = curr.options;
        return acc;
      }, {});
      setFormOptions(newOptions);
    };

    fetchOptions();
  }, []);

  const handleInputChange = (value, key) => {
    setFormData({ ...formData, [key]: value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form Data:', formData);
  };

  const handleReset = () => {
    setFormData({});
  };

  return (
    <Form onFinish={handleSubmit}>
      {Object.entries(formFieldsConfig).map(([key, { type, label, options, required, dependentOn }]) => {
        let Component;
        let inputProps = {
          key: key,
          label: label,
          name: key,
          rules: [{ required }],
          onChange: (e) => handleInputChange(e.target.value, key)
        };

        switch (type) {
          case 'select':
            Component = Select;
            inputProps.onChange = (value) => handleInputChange(value, key);
            break;
          case 'checkboxGroup':
            Component = CheckboxGroup;
            break;
          case 'textInput':
            Component = TextInput;
            break;
          case 'datePicker':
            Component = DatePicker;
            inputProps.onChange = (date, dateString) => handleInputChange(dateString, key);
            break;
          default:
            Component = TextInput;
        }

        return (
          (!dependentOn || formData[dependentOn] === 'true') && (
            <FormItem {...inputProps}>
              {Component === Select || Component === CheckboxGroup ? (
                options.map(option => (
                  <Component.Option key={option.value} value={option.value}>
                    {option.label}
                  </Component.Option>
                ))
              ) : (
                <Component />
              )}
            </FormItem>
          )
        );
      })}
      <FormItem>
        <Button type="primary" htmlType="submit">Submit</Button>
        <Button htmlType="button" onClick={handleReset}>Reset</Button>
      </FormItem>
    </Form>
  );
};

export default BillLogMonitoring;
