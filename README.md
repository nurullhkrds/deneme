 <Form.Item
        label={ReturnMapFormLocale.bankReturnCode.label}
        rules={[{ required: true, message: ReturnMapFormLocale.messages.bankReturnCodeValidated }]}
      >
        <Select
          value={bankReturnCode}
          onChange={handleBankReturnCodeChange}
          showSearch
          placeholder="Select a bank return code"
          optionFilterProp="children"
        >
          {bankReturnData.map((item) => (
            <Option key={item.code} value={item.code}>
              {item.code}
            </Option>
          ))}
        </Select>
      </Form.Item>

      {/* Bank Return Text Select */}
      <Form.Item
        label={ReturnMapFormLocale.bankReturnText.label}
        rules={[{ required: true, message: ReturnMapFormLocale.messages.bankReturnTextValidated }]}
      >
        <Select
          value={bankReturnText}
          onChange={handleBankReturnTextChange}
          showSearch
          placeholder="Select a bank return text"
          optionFilterProp="children"
        >
          {bankReturnData.map((item) => (
            <Option key={item.text} value={item.text}>
              {item.text}
            </Option>
          ))}
        </Select>
      </Form.Item>


const handleBankReturnCodeChange = (value) => {
    setBankReturnCode(value);
    const foundText = bankReturnData.find((item) => item.code === value)?.text || '';
    setBankReturnText(foundText);
  };

  // Handle changes for bankReturnText and automatically update the corresponding code
  const handleBankReturnTextChange = (value) => {
    setBankReturnText(value);
    const foundCode = bankReturnData.find((item) => item.text === value)?.code || '';
    setBankReturnCode(foundCode);
  };
