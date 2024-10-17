const ValidateFieldsForm = () => {
  const formRef = React.useRef(null);
  useEffect(() => {
    if (formRef.current)
      formRef.current.validateFields(null, (error, values) => {
        if (!error) {
          console.log('ok', values);
        } else {
          console.log('error', error, values);
        }
      });
  }, []);

  const handleSubmit = (e, error, values) => {
    e.preventDefault();
    if (!error) {
      // Do sth. with values
      alert(JSON.stringify(values));
    }
  };

  const userExists = () => {
    console.log("User doesn't exist: ");
  };

  return (
    <Form ref={formRef} onSubmit={handleSubmit}>
      <FormItem label="Username">
        <TextInput
          name="username"
          validation={[{ required: true }, { min: 2, max: 5 }]}
        />
      </FormItem>
      <FormItem label="Password">
        <TextInput
          type="password"
          name="password"
          validation={[{ required: true }]}
        />
      </FormItem>
      <FormItem label="Gender">
        <Select
          name="gender"
          validation={[{ required: true }]}
          data={[
            {
              value: 'male',
              label: 'Male',
            },
            {
              value: 'female',
              label: 'Female',
            },
          ]}
        />
      </FormItem>
      <FormItem label="Email">
        {/* Email input has `email` validation by default when used in Form component (but not required by default) */}
        <EmailInput type="email" name="email" />
      </FormItem>
      <FormItem label="Age">
        <NumberInput
          name="age"
          validation={[
            { required: true },
            { type: 'number', min: 18, max: 30 },
          ]}
        />
      </FormItem>
      <FormItem label="">
        <Button type="primary" htmlType="submit">
          Log in
        </Button>
      </FormItem>
    </Form>
  );
};

<ValidateFieldsForm />;
