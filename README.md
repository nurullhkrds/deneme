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


import React, { useState } from 'react';
import { Button, Form, Select, CheckboxGroup, TextInput, DatePicker } from 'ykb-ui';
import { formFieldsConfig } from './formConfig';
const FormItem = Form.Item;

function BillLogMonitoring() {
  const [formData, setFormData] = useState({});
  const [isMicro, setIsMicro] = useState('false');

  const handleChange = (name, value) => {
    setFormData(prev => ({ ...prev, [name]: value }));
    if (name === 'isMicro') {
      setIsMicro(value);
    }
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Submitted Form Data:', formData);
  };

  const resetForm = () => {
    setFormData({});
    setIsMicro('false');
  };

  return (
    <Form onSubmit={handleSubmit}>
      {Object.entries(formFieldsConfig).map(([key, config]) => (
        <FormItem label={config.label} key={key} required={config.required}>
          {config.type === 'select' && (
            <Select name={key} onChange={(value) => handleChange(key, value)}>
              {config.options.map(option => (
                <Select.Option key={option.value} value={option.value}>{option.label}</Select.Option>
              ))}
            </Select>
          )}
          {config.type === 'checkboxGroup' && (
            <CheckboxGroup name={key} onChange={(value) => handleChange(key, value)}>
              {config.options.map(item => (
                <CheckboxGroup.Item key={item.value} value={item.value} label={item.label} />
              ))}
            </CheckboxGroup>
          )}
          {config.type === 'datePicker' && (
            <DatePicker name={key} onChange={(value) => handleChange(key, value)} />
          )}
          {config.type === 'textInput' && (
            <TextInput name={key} onChange={(value) => handleChange(key, value)} />
          )}
          {config.dependentOn === 'isMicro' && isMicro === 'true' && (
            <TextInput name={key} required={config.required} onChange={(value) => handleChange(key, value)} />
          )}
        </FormItem>
      ))}
      <Button onClick={resetForm} type="secondary">Reset</Button>
      <Button type="submit" type="primary">Submit</Button>
    </Form>
  );
}

export default BillLogMonitoring;

