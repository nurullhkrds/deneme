 <Select
              showSearch
              allowClear
              value={isReversible} // Seçili değer state ile kontrol ediliyor
              onChange={handleSelectReversible}
            >
              <Option value={true}>Evet</Option> {/* true değeri gönderir */}
              <Option value={false}>Hayır</Option> {/* false değeri gönderir */}
            </Select>
