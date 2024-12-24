import React, { useEffect } from 'react';
import { Row, Col, Button, Modal, Form, Select, Notification, Checkbox, Icon, Message, DatePicker, Textarea, CheckboxGroup, NumberInput, TextInput } from 'ykb-ui';
const FormItem = Form.Item;

const initData = {
  urun: 'DOĞALGAZ',
  kurum: '1000001',
  odemeTipi: ['Kredi Kartı'],
  bildirimDurumu: 'Evet',
  faturaNo: '',
  odemeTarihi: moment(),
};

function BillLogMonitoring() {
  const formRef = React.useRef(null);

  useEffect(() => {
    if (formRef.current) {
      formRef.current.setFieldsValue(initData);
    }
  }, []);

  const reset = e => {
    e.preventDefault();
    formRef.current.resetFields();
  };

  const onSubmit = (e, errors, values) => {
    if (!errors) {
      console.log('ok', 'test submit', values);
    } else {
      console.log('error', 'test submit', errors, values);
    }
  };

  return (
    <Form ref={formRef} onSubmit={onSubmit} colSpan={{ span: 12 }}>
      <FormItem label="Ürün Türü">
        <Select
          name="urun"
          data={[
            { value: 'DOĞALGAZ', label: 'Doğalgaz' },
            { value: 'İGDAŞ', label: 'İGDAŞ' },
          ]}
        />
      </FormItem>

      <FormItem label="Kurum">
        <Select
          name="kurum"
          data={[
            { value: '1000001', label: '1000001' },
            { value: '1000002', label: '1000002' },
          ]}
        />
      </FormItem>

      <FormItem label="Fatura Ödeme Tipi">
        <CheckboxGroup
          name="odemeTipi"
          items={[
            { value: 'Kredi Kartı', textLabel: 'Kredi Kartı' },
            { value: 'Hesap', textLabel: 'Hesap' },
            { value: 'Vezne', textLabel: 'Vezne' },
          ]}
        />
      </FormItem>

      <FormItem label="Fatura Ödeme Bildirim Durumu">
        <Select
          name="bildirimDurumu"
          data={[
            { value: 'Evet', label: 'Evet' },
            { value: 'Hayır', label: 'Hayır' },
          ]}
        />
      </FormItem>

      <FormItem label="Fatura No">
        <TextInput name="faturaNo" />
      </FormItem>

      <FormItem label="Ödeme Tarihi">
        <DatePicker name="odemeTarihi" format="DD/MM/YYYY" validation={[{ required: true }]} />
      </FormItem>

      <FormItem colSpan={{ span: 24 }}>
        <Button onClick={reset} type="secondary">
          Reset
        </Button>
        <Button htmlType="submit" type="primary">
          Submit
        </Button>
      </FormItem>
    </Form>
  );
}

export default BillLogMonitoring;
