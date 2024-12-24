import React, { useEffect } from 'react';
import { Button, Form, Select, CheckboxGroup, TextInput, DatePicker } from 'ykb-ui';
const FormItem = Form.Item;

function BillLogMonitoring() {
  const formRef = React.useRef(null);

  useEffect(() => {
    if (formRef.current) {
      formRef.current.setFieldsValue({
        urun: 'DOĞALGAZ',
        kurum: '1000001',
        odemeTipi: ['Kredi Kartı'],
        bildirimDurumu: 'Evet',
        bildirimBaslangicTarihi: moment(),
        bildirimBitisTarihi: moment().add(7, 'days'),
        odemeTarihi: moment(),
        faturaNo: '',
        aboneNo: '',
        isMicro: 'false', // Default as string to match Select option values
        returnMapCode: ''
      });
    }
  }, []);

  const reset = e => {
    e.preventDefault();
    formRef.current.resetFields();
  };

  const onSubmit = (e, errors, values) => {
    e.preventDefault();
    // Convert isMicro back to boolean before submitting or processing
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
          <Select.Option value="DOĞALGAZ">Doğalgaz</Select.Option>
          <Select.Option value="İGDAŞ">İGDAŞ</Select.Option>
        </Select>
      </FormItem>

      <FormItem label="Kurum">
        <Select name="kurum" validation={[{ required: true }]}>
          <Select.Option value="1000001">1000001</Select.Option>
          <Select.Option value="1000002">1000002</Select.Option>
        </Select>
      </FormItem>

      <FormItem label="Fatura Ödeme Tipi">
        <CheckboxGroup name="odemeTipi" validation={[{ required: true }]}>
          <CheckboxGroup.Item value="Kredi Kartı" label="Kredi Kartı" />
          <CheckboxGroup.Item value="Hesap" label="Hesap" />
          <CheckboxGroup.Item value="Vezne" label="Vezne" />
        </CheckboxGroup>
      </FormItem>

      <FormItem label="Fatura Ödeme Bildirim Durumu">
        <Select name="bildirimDurumu" validation={[{ required: true }]}>
          <Select.Option value="Evet">Evet</Select.Option>
          <Select.Option value="Hayır">Hayır</Select.Option>
        </Select>
      </FormItem>

      <FormItem label="Ödeme Bildirim Başlangıç Tarihi">
        <DatePicker name="bildirimBaslangicTarihi" format="DD/MM/YYYY" validation={[{ required: true }]}/>
      </FormItem>

      <FormItem label="Ödeme Bildirim Bitiş Tarihi">
        <DatePicker name="bildirimBitisTarihi" format="DD/MM/YYYY" validation={[{ required: true }]}/>
      </FormItem>

      <FormItem label="Fatura No">
        <TextInput name="faturaNo" />
      </FormItem>

      <FormItem label="Abone No">
        <TextInput name="aboneNo" />
      </FormItem>

      <FormItem label="Ödeme Tarihi">
        <DatePicker name="odemeTarihi" format="DD/MM/YYYY" />
      </FormItem>

      <FormItem label="Micro Status">
        <Select name="isMicro" validation={[{ required: true }]}>
          <Select.Option value="true">Evet</Select.Option>
          <Select.Option value="false">Hayır</Select.Option>
        </Select>
      </FormItem>

      <FormItem label="Return Map Code">
        <TextInput name="returnMapCode" validation={[{ required: true }]}/>
      </FormItem>

      <FormItem>
        <Button onClick={reset} type="secondary">Reset</Button>
        <Button htmlType="submit" type="primary">Submit</Button>
      </FormItem>
    </Form>
  );
}

export default BillLogMonitoring;
