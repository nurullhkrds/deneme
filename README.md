import React, { useState } from 'react';

// Dummy data and components - Gerçek UI kütüphanenize göre değiştirin
const DummySelect = ({ options, onChange, value }) => (
  <select onChange={e => onChange(e.target.value)} value={value}>
    {options.map(opt => (
      <option key={opt.value} value={opt.value}>{opt.label}</option>
    ))}
  </select>
);

const DummyTextInput = ({ onChange, value }) => (
  <input type="text" onChange={e => onChange(e.target.value)} value={value} />
);

const DummyCheckboxGroup = ({ options, onChange, values }) => (
  <div>
    {options.map(opt => (
      <label key={opt.value}>
        <input
          type="checkbox"
          checked={values.includes(opt.value)}
          onChange={e => onChange(e.target.checked ? opt.value : null)}
        />
        {opt.label}
      </label>
    ))}
  </div>
);

const formFieldsConfig = [
  { name: 'urun', label: 'Ürün Türü', type: 'select', options: [{ value: 'dogalgaz', label: 'Doğalgaz' }, { value: 'igdas', label: 'İGDAŞ' }]},
  { name: 'kurum', label: 'Kurum', type: 'select', options: [{ value: '1000001', label: '1000001' }, { value: '1000002', label: '1000002' }]},
  { name: 'odemeTipi', label: 'Fatura Ödeme Tipi', type: 'checkbox', options: [{ value: 'krediKarti', label: 'Kredi Kartı' }, { value: 'hesap', label: 'Hesap' }]},
  { name: 'faturaNo', label: 'Fatura No', type: 'text'},
];

function BillLogMonitoring() {
  const [formData, setFormData] = useState({});

  const handleFieldChange = (field, value) => {
    setFormData(prev => ({ ...prev, [field]: value }));
  };

  const handleSubmit = (event) => {
    event.preventDefault();
    console.log('Submitted Data:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      {formFieldsConfig.map(field => (
        <div key={field.name}>
          <label>{field.label}</label>
          {field.type === 'select' && (
            <DummySelect
              options={field.options}
              onChange={value => handleFieldChange(field.name, value)}
              value={formData[field.name] || ''}
            />
          )}
          {field.type === 'text' && (
            <DummyTextInput
              onChange={value => handleFieldChange(field.name, value)}
              value={formData[field.name] || ''}
            />
          )}
          {field.type === 'checkbox' && (
            <DummyCheckboxGroup
              options={field.options}
              onChange={value => {
                const newValues = formData[field.name] ? [...formData[field.name]] : [];
                if (newValues.includes(value)) {
                  const index = newValues.indexOf(value);
                  newValues.splice(index, 1);
                } else if (value) {
                  newValues.push(value);
                }
                handleFieldChange(field.name, newValues);
              }}
              values={formData[field.name] || []}
            />
          )}
        </div>
      ))}
      <button type="submit">Submit</button>
    </form>
  );
}

export default BillLogMonitoring;
