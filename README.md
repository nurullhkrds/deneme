   <Form.Item label={ReturnMapFormLocale.bankReturnCode.label}>
            <input
              validation={[{ required: true }]}
              name="bankReturnCode"
              value={bankReturnCode}
              onChange={(e) => {
                const value = e.target.value;
                if (/^\d*$/.test(value)) {
                  handleBankReturnCode(e); // Sadece rakamlar olduğunda state'i güncelle
                } else {
                  Notification.warning(ReturnMapFormLocale.messages.bankReturnCodeValidated);
                }
              }}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>

          <Form.Item label={ReturnMapFormLocale.bankRetubankReturnTextrnCode.label}>
            <input
              validation={[{ required: true }]}
              name="bankReturnText"
              value={bankReturnText}
              onChange={handleBankReturnText}
              style={{ border: '1px solid #dcdcdc', borderRadius: '4px', padding: '8px', fontSize: '14px', width: '100%' }}
            />
          </Form.Item>
