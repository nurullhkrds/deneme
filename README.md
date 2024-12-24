import React, { useEffect, useState } from 'react';
import { Button, Form, Select, CheckboxGroup, TextInput, DatePicker } from 'ykb-ui';
import { formFieldsConfig } from './formFieldsConfig';
const FormItem = Form.Item;

function BillLogMonitoring() {
  const formRef = React.useRef(null);
  const [formOptions, setFormOptions] = useState({});
  const [formData, setFormData] = useState({});
  const [isMicro, setIsMicro] = useState(false);

  useEffect(() => {
    async function fetchOptions() {
      const optionsData = {};
      for (const field in formFieldsConfig) {
        if (formFieldsConfig[field].optionsEndPoint) {
          const response = await fetch(formFieldsConfig[field].optionsEndPoint);
          optionsData[field] = await response.json();
        }
      }
      setFormOptions(optionsData);
    }

    fetchOptions();
  }, []);

  const handleChange = (name, value) => {
    if (name === 'isMicro') {
      setIsMicro(value === 'true');
    }
    setFormData({ ...formData, [name]: value });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    // Process form submission here
    console.log(formData);
  };

  return (
    <Form ref={formRef} onSubmit={handleSubmit}>
      {Object.entries(formFieldsConfig).map(([key, config]) => {
        const Component = config.type === 'select' ? Select : config.type === 'checkboxGroup' ? CheckboxGroup : config.type === 'datePicker' ? DatePicker : TextInput;
        const options = formOptions[key] || config.options;

        return (
          <FormItem label={config.label} key={key} required={config.required}>
            <Component name={key} options={options} onChange={(value) => handleChange(key, value)} validation={config.validation}>
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
