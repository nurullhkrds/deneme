// Örnek yapılandırma dosyası
export const formFieldsConfig = {
  urun: {
    type: 'select',
    label: 'Ürün Türü',
    required: true,
    optionsEndPoint: '/api/urun-options'
  },
  kurum: {
    type: 'select',
    label: 'Kurum',
    required: true,
    optionsEndPoint: '/api/kurum-options'
  },
  odemeTipi: {
    type: 'checkboxGroup',
    label: 'Fatura Ödeme Tipi',
    required: true,
    optionsEndPoint: '/api/odeme-tipi-options'
  },
  bildirimDurumu: {
    type: 'select',
    label: 'Fatura Ödeme Bildirim Durumu',
    required: true,
    optionsEndPoint: '/api/bildirim-durumu-options'
  },
  isMicro: {
    type: 'select',
    label: 'Micro Status',
    required: true,
    options: [
      { value: 'true', label: 'Evet' },
      { value: 'false', label: 'Hayır' }
    ]
  },
  returnMapCode: {
    type: 'textInput',
    label: 'Return Map Code',
    requiredDependent: 'isMicro', // Bu alan 'isMicro' true olduğunda gereklidir
    required: false
  }
};
