import React, { useEffect, useState } from 'react';
import { Button, Form, Select, CheckboxGroup, TextInput, DatePicker } from 'ykb-ui';
const FormItem = Form.Item;

function BillLogMonitoring() {
  const formRef = React.useRef(null);
  const [formOptions, setFormOptions] = useState({});
  const [isMicro, setIsMicro] = useState('false');

  // API'den dinamik verileri çekme ve form seçeneklerini doldurma
  useEffect(() => {
    async function fetchFormOptions() {
      try {
        const response = await fetch('https://api.example.com/form-options');
        const data = await response.json();

        setFormOptions({
          urun: data.urunOptions,
          kurum: data.kurumOptions,
          odemeTipi: data.odemeTipiOptions,
          bildirimDurumu: data.bildirimDurumuOptions,
          // Diğer form elemanları için gerekirse başka veriler de ekleyebilirsiniz
        });

        // Form başlangıç değerlerini ayarla
        if (formRef.current) {
          formRef.current.setFieldsValue({
            urun: data.urunOptions[0].value,
            kurum: data.kurumOptions[0].value,
            odemeTipi: [data.odemeTipiOptions[0].value],
            bildirimDurumu: data.bildirimDurumuOptions[0].value,
            isMicro: 'false',
            returnMapCode: ''
          });
        }
      } catch (error) {
        console.error('Failed to fetch form options', error);
      }
    }

    fetchFormOptions();
  }, []);

  // Form sıfırlama işlevi
  const reset = e => {
    e.preventDefault();
    formRef.current.resetFields();
    setIsMicro('false');
  };

  // Form gönderme işlevi
  const onSubmit = (e, errors, values) => {
    e.preventDefault();
    // isMicro durumunu boolean olarak ayarla
    values.isMicro = values.isMicro === 'true';
    if (!errors) {
      console.log('Form Submitted', values);
    } else {
      console.log('Submission errors', errors, values);
    }
  };

  return (
    <Form ref={formRef} onSubmit={onSubmit}>
      <FormItem label="Ürün Türü">
        <Select name="urun" validation={[{ required: true }]}>
          {formOptions.urun && formOptions.urun.map(option => (
            <Select.Option key={option.value} value={option.value}>{option.label}</Select.Option>
          ))}
        </Select>
      </FormItem>

      <FormItem label="Kurum">
        <Select name="kurum" validation={[{ required: true }]}>
          {formOptions.kurum && formOptions.kurum.map(option => (
            <Select.Option key={option.value} value={option.value}>{option.label}</Select.Option>
          ))}
        </Select>
      </FormItem>

      <FormItem label="Fatura Ödeme Tipi">
        <CheckboxGroup name="odemeTipi" validation={[{ required: true }]}>
          {formOptions.odemeTipi && formOptions.odemeTipi.map(item => (
            <CheckboxGroup.Item key={item.value} value={item.value} label={item.label} />
          ))}
        </CheckboxGroup>
      </FormItem>

      <FormItem label="Fatura Ödeme Bildirim Durumu">
        <Select name="bildirimDurumu" validation={[{ required: true }]}>
          {formOptions.bildirimDurumu && formOptions.bildirimDurumu.map(option => (
            <Select.Option key={option.value} value={option.value}>{option.label}</Select.Option>
          ))}
        </Select>
      </FormItem>

      <FormItem label="Micro Status">
        <Select name="isMicro" onChange={value => setIsMicro(value)} validation={[{ required: true }]}>
          <Select.Option value="true">Evet</Select.Option>
          <Select.Option value="false">Hayır</Select.Option>
        </Select>
      </FormItem>

      {isMicro === 'true' && (
        <FormItem label="Return Map Code">
          <TextInput name="returnMapCode" validation={[{ required: true, message: 'Return map code is required when micro status is "Yes".' }]} />
        </FormItem>
      )}

      <FormItem>
        <Button onClick={reset} type="secondary">Reset</Button>
        <Button htmlType="submit" type="primary">Submit</Button>
      </FormItem>
    </Form>
  );
}

export default BillLogMonitoring;
