const ValidateFieldsForm = () => {
  const formRef = React.useRef(null);
  const [validate, setValidate] = React.useState(false);

  const handleSubmit = (e, error, values) => {
    e.preventDefault();
    if (!error) {
      // Do sth. with values
      alert(JSON.stringify(values));
    } else {
      console.log(error);
    }
  };

  const onValuesChange = changedValues => {
    if ('country' in changedValues) {
      setValidate(changedValues.country === 'usa');
    }
  };

  return (
    <Form ref={formRef} onSubmit={handleSubmit} onValuesChange={onValuesChange}>
      <FormItem label="Name">
        <TextInput
          name="name"
          validation={[{ required: true }, { min: 2, max: 5 }]}
        />
      </FormItem>
      <FormItem label="Surname">
        <TextInput name="nurname" validation={[{ required: true }]} />
      </FormItem>
      <FormItem label="country">
        <Select
          allowClear={true}
          name="country"
          validation={[{ required: true }]}
          data={[
            {
              value: 'tr',
              label: 'Turkey',
            },
            {
              value: 'usa',
              label: 'USA',
            },
          ]}
        />
      </FormItem>
      <FormItem label="Post Code">
        <TextInput name="postCode" validation={[{ required: validate }]} />
      </FormItem>
      <FormItem label="">
        <Button type="primary" htmlType="submit">
          Submit
        </Button>
      </FormItem>
    </Form>
  );
};

<ValidateFieldsForm />;
