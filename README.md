 const handleClickOneDelete = async (record) => {
    try {

      const institutions = record?.returnMapDefinition?.institutions || [];
      let contentMessage;
      if (institutions.length === 1) {
        contentMessage = `${institutions[0]} kurumu bu returnMap'i kullanmakta.Önce bağımlılığınızı temizleyiniz.`;
      } else if (institutions.length > 1) {
        contentMessage = `${institutions.join(', ')} kurumları bu returnMap'i kullanmakta.Önce bağımlılıklarınızı temizleyiniz.. `;
      } else {
        contentMessage = ReturnMapFormLocale.messages.deleteContent;
      }


      Message.warning({
        title: ReturnMapFormLocale.messages.deleteTitle,
        content: contentMessage,
      });
    } catch (error) {
      console.error('Error fetching return map by id:', error);
    }
  };
