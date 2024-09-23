    <Form.Item
        label="Bank Return Code"
        rules={[{ required: true, message: 'Please select a bank return code!' }]}
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
        label="Bank Return Text"
        rules={[{ required: true, message: 'Please select a bank return text!' }]}
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
    // Automatically set the corresponding text when a code is selected
    const foundText = bankReturnData.find((item) => item.code === value)?.text || '';
    setBankReturnText(foundText);
  };

  const handleBankReturnTextChange = (value) => {
    setBankReturnText(value);
    // Automatically set the corresponding code when text is selected
    const foundCode = bankReturnData.find((item) => item.text === value)?.code || '';
    setBankReturnCode(foundCode);
  };




import { bankReturnData } from './BankReturnData'; // Assuming this contains the bank return code and text data
