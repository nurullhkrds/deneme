 
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
        // eslint-disable-next-line no-undef
        bildirimBaslangicTarihi: moment(),
        // eslint-disable-next-line no-undef
        bildirimBitisTarihi: moment().add(7, 'days'),
        // eslint-disable-next-line no-undef
        odemeTarihi: moment(),
        faturaNo: '',
        aboneNo: ''
      });
    }
  }, []);

  const reset = e => {
    e.preventDefault();
    formRef.current.resetFields();
  };

  const onSubmit = (e, errors, values) => {
    if (!errors) {
      console.log('Form Submitted', values);
    } else {
      console.log('Submission errors', errors, values);
    }
  };

  return (
    <Form ref={formRef} onSubmit={onSubmit}>
      <FormItem label="Ürün Türü">
        <Select name="urun"
          data={[{ value: 'DOĞALGAZ', label: 'Doğalgaz' },
          { value: 'İGDAŞ', label: 'İGDAŞ' }]} validation={[{ required: true }]} />
      </FormItem>

      <FormItem label="Kurum">
        <Select name="kurum"
          data={[{ value: '1000001', label: '1000001' },
          { value: '1000002', label: '1000002' }]} validation={[{ required: true }]}/>
      </FormItem>

      <FormItem label="Fatura Ödeme Tipi">
        <CheckboxGroup name="odemeTipi"
          items={[{ value: 'Kredi Kartı', textLabel: 'Kredi Kartı' },
          { value: 'Hesap', textLabel: 'Hesap' },
          { value: 'Vezne', textLabel: 'Vezne' }]} validation={[{ required: true }]}/>
      </FormItem>

      <FormItem label="Fatura Ödeme Bildirim Durumu">
        <Select name="bildirimDurumu" 
        data={[{ value: 'Evet', label: 'Evet' },
           { value: 'Hayır', label: 'Hayır' }]} validation={[{ required: true }]}/>
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

      <FormItem>
        <Button onClick={reset} type="secondary">Reset</Button>
        <Button htmlType="submit" type="primary">Submit</Button>
      </FormItem>
    </Form>
  );
}

export default BillLogMonitoring;


  @NotNull
    @Schema(description = "Micro status", example = "Y,N", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isMicro;

    @Schema(description = "Micro status", example = "Y,N", requiredMode = Schema.RequiredMode.REQUIRED)
    private String returnMapCode;
