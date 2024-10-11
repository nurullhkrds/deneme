  <Select
              value={isReversible}
              defaultValue={undefined}
              onChange={handleSelectReversible}
              validation={[{ required: true }]}
            >
              <Select.Option value="true">Evet</Select.Option>
              <Select.Option value="false">Hayır</Select.Option>
            </Select>
