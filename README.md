const handleClickOneDefinitionDelete = async (record) => {
    try {
      console.log(record);

      // Institutions listesini kontrol et
      const institutions = record.institutions;
      let contentMessage;

      if (institutions.length === 1) {
        contentMessage = `${institutions[0]} kurumu bu returnMap'i kullanmakta. Silmek istediğinize emin misiniz?`;
      } else if (institutions.length > 1) {
        contentMessage = `${institutions.join(', ')} kurumları bu returnMap'i kullanmakta. Silmek istediğinize emin misiniz?`;
      } else {
        contentMessage = 'Kaydı silmek istediğinize emin misiniz?';
      }

      // Mesajı prompt olarak göster
      Message.prompt({
        title: 'Kayıt Silme',
        content: contentMessage,
        icon: <Icon name="warning-circle" colorType="warning" />,
        onClose: () => {
          console.log('onClose');
        },
        onOk: () => {
          console.log('onOk');
        },
        onCancel: () => {
          console.log('onCancel');
        },
        okText: 'Evet',
        cancelText: 'Vazgeç',
      });
    } catch (error) {
      console.error('Error fetching return map by id:', error);
    }
  };
