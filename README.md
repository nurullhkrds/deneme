{
  "success": true,
  "message": "Veri getirildi",
  "statusCode": 200,
  "data": {
    "id": 100095,
    "returnMapCode": "TAHSILAT_ITO_ALL",
    "isActive": true,
    "institutions": [
      "İstanbul Ticaret Odası"
    ]
  }
}


const handleClickOneDefinitionDelete = async (record) => {
    try {
      console.log(record)
      Message.prompt({
        title: 'Kayıt Silme',
        content: 'Kaydı silmek istediğinize emin misiniz ?',
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
