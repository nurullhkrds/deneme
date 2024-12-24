import React, { useEffect, useState, useRef } from 'react';
import { Button, Form, Select, CheckboxGroup, TextInput, DatePicker, Checkbox } from 'ykb-ui';
import { formFieldsConfig } from './formFieldsConfig';

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
      <Button type="submit" >Submit</Button>
    </Form>
  );
}

export default BillLogMonitoring;




// formConfig.js
export const formFieldsConfig = {
  urun: {
    type: 'select',
    label: 'Ürün Türü',
    optionsEndPoint: '/api/urun-options',
    required: true
  },
  kurum: {
    type: 'select',
    label: 'Kurum',
    optionsEndPoint: '/api/kurum-options',
    required: true
  },
  odemeTipi: {
    type: 'checkboxGroup',
    label: 'Fatura Ödeme Tipi',
    options: [
      { value: 'K', label: 'K (Kredi Kartı)' },
      { value: 'O', label: 'O (Hesap)' },
      { value: 'V', label: 'V (Vezne)' },
      { value: 'I', label: 'I (İnternet Bankacılığı)' },
      { value: 'A', label: 'A (ATM)' },
      { value: 'H', label: 'Hepsi' }
    ],
    required: true
  },
  bildirimDurumu: {
    type: 'select',
    label: 'Fatura Ödeme Bildirim Durumu',
    options: [
      { value: 'Evet', label: 'Evet' },
      { value: 'Hayır', label: 'Hayır' }
    ],
    required: true
  },
  bildirimBaslangicTarihi: {
    type: 'datePicker',
    label: 'Ödeme Bildirim Başlangıç Tarihi',
    required: true
  },
  bildirimBitisTarihi: {
    type: 'datePicker',
    label: 'Ödeme Bildirim Bitiş Tarihi',
    required: true
  },
  faturaNo: {
    type: 'textInput',
    label: 'Fatura No'
  },
  aboneNo: {
    type: 'textInput',
    label: 'Abone No'
  },
  odemeTarihi: {
    type: 'datePicker',
    label: 'Ödeme Tarihi'
  },
  isMicro: {
    type: 'select',
    label: 'Micro Status',
    options: [
      { value: 'true', label: 'Evet' },
      { value: 'false', label: 'Hayır' }
    ],
    required: true
  },
  returnMapCode: {
    type: 'textInput',
    label: 'Return Map Code',
    dependentOn: 'isMicro',
    required: true
  }
};
