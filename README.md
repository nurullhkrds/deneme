  rules={[{ required: true, message: 'Lütfen bir dönüş kodu seçin!' }]}
 if (!selectedDefinitionId) {
    // Zorunlu alan doldurulmamışsa uyarı verin
    message.error('Lütfen bir dönüş kodu seçin!');
    return;
  }
