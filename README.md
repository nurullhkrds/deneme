// formConfig.js
export const formFieldsConfig = {
  urun: {
    type: 'select',
    label: 'Ürün Türü',
    optionsEndPoint: '/api/urun-options',
    required: true
  },
  kurum: {
    type: 'select',
    label: 'Kurum',
    optionsEndPoint: '/api/kurum-options',
    required: true
  },
  odemeTipi: {
    type: 'checkboxGroup',
    label: 'Fatura Ödeme Tipi',
    options: [
      { value: 'K', label: 'K (Kredi Kartı)' },
      { value: 'O', label: 'O (Hesap)' },
      { value: 'V', label: 'V (Vezne)' },
      { value: 'I', label: 'I (İnternet Bankacılığı)' },
      { value: 'A', label: 'A (ATM)' },
      { value: 'H', label: 'Hepsi' }
    ],
    required: true
  },
  bildirimDurumu: {
    type: 'select',
    label: 'Fatura Ödeme Bildirim Durumu',
    options: [
      { value: 'Evet', label: 'Evet' },
      { value: 'Hayır', label: 'Hayır' }
    ],
    required: true
  },
  bildirimBaslangicTarihi: {
    type: 'datePicker',
    label: 'Ödeme Bildirim Başlangıç Tarihi',
    required: true
  },
  bildirimBitisTarihi: {
    type: 'datePicker',
    label: 'Ödeme Bildirim Bitiş Tarihi',
    required: true
  },
  faturaNo: {
    type: 'textInput',
    label: 'Fatura No'
  },
  aboneNo: {
    type: 'textInput',
    label: 'Abone No'
  },
  odemeTarihi: {
    type: 'datePicker',
    label: 'Ödeme Tarihi'
  },
  isMicro: {
    type: 'select',
    label: 'Micro Status',
    options: [
      { value: 'true', label: 'Evet' },
      { value: 'false', label: 'Hayır' }
    ],
    required: true
  },
  returnMapCode: {
    type: 'textInput',
    label: 'Return Map Code',
    dependentOn: 'isMicro',
    required: true
  }
};
