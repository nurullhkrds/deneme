import React, { useEffect } from 'react';
import { Row, Col, Button, Modal, Form, Select, Notification, Checkbox, Icon, Message, DatePicker, Textarea, CheckboxGroup, NumberInput, TextInput } from 'ykb-ui';
const FormItem = Form.Item;


const initData = {
  textinput: 'Rasit',
  numberinput: 10,
  select: 'rasit',
  checkboxGroupTest: ['b'],
  // eslint-disable-next-line no-undef
  datepicker: moment(),
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
      <FormItem label="TextInput">
        <TextInput name="textinput" />
      </FormItem>

      <FormItem label="Number">
        <NumberInput name="numberinput" mobile={true} />
      </FormItem>

      <FormItem label="Select">
        <Select
          name="select"
          data={[
            {
              value: 'rasit',
              label: 'Rasit',
            },
            {
              value: 'enis',
              label: 'Enis',
            },
            {
              value: 'ebru',
              label: 'Ebru',
            },
          ]}
        />
      </FormItem>

      <FormItem label="Checkbox Group">
        <CheckboxGroup
          onChange={data => {
            console.log(data);
          }}
          name="checkboxGroupTest"
          items={[
            { value: 'a', textLabel: 'Option 1' },
            { value: 'b', textLabel: 'Option 2' },
          ]}
        />
      </FormItem>

      <Form.Item label="Date">
        <DatePicker name="datepicker" validation={[{ required: true }]} />
      </Form.Item>

      <FormItem label="Textarea">
        <Textarea name="textarea" />
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
