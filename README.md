import { Typography } from 'ykb-ui';
const { TextInput } = Typography;
import {Card, Row,Button, Col,Tabs, Modal, Form, Select, Notification, Checkbox, Icon, Message,Layout,Header,Space,Title ,theme,TextInputInput } 
from 'ykb-ui';
import { Form, Select, TextInput, DatePicker, Textarea, Button, Row, InfoBox } from 'ykb-ui';

import { Button, Icon, Table, TableHeader } from 'ykb-ui';

const institutionOptions = [
  "Enerjisa Müşteri Çözümleri",
  "BEDAŞ",
  "İGDAŞ",
  "İSKİ",
  "Türk Telekom",
  "Turkcell",
  "Vodafone",
  "Türksat",
  "ASKİ",
  "CLK Akdeniz"
];

const cityOptions = ["İstanbul", "Ankara", "İzmir", "Bursa", "Antalya"];
const productOptions = ["Tahsilat", "Elektrik", "Su", "Doğalgaz", "CepTel"];
const debtTypeOptions = ["Fatura", "Tahsilat", "Harç", "Abonelik"];
const serviceTypeOptions = ["Seçiniz", "Bireysel", "Kurumsal"];

const dynamicFieldConfig = {
  "Enerjisa Müşteri Çözümleri": {
    label: "Sözleşme No",
    placeholder: "Sözleşme numarası giriniz"
  },
  BEDAŞ: {
    label: "Tesisat No",
    placeholder: "Tesisat numarası giriniz"
  },
  İGDAŞ: {
    label: "Sözleşme Hesap No",
    placeholder: "Sözleşme hesap numarası giriniz"
  },
  İSKİ: {
    label: "Abone No",
    placeholder: "Abone numarası giriniz"
  },
  Turkcell: {
    label: "Telefon Numarası",
    placeholder: "5XX XXX XX XX"
  },
  Vodafone: {
    label: "Telefon Numarası",
    placeholder: "5XX XXX XX XX"
  },
  "Türk Telekom": {
    label: "Hizmet Numarası",
    placeholder: "Hizmet numarası giriniz"
  },
  Türksat: {
    label: "Abone Numarası",
    placeholder: "Abone numarası giriniz"
  },
  ASKİ: {
    label: "Abone No",
    placeholder: "Abone numarası giriniz"
  },
  "CLK Akdeniz": {
    label: "Sözleşme No",
    placeholder: "Sözleşme numarası giriniz"
  }
};

const initialBills = [
  {
    key: 1,
    customerName: "AYDIN İNŞ. TİC. TURİZM VE SAN. LTD. ŞTİ.",
    invoiceNo: "000000022295",
    debtType: "Elektrik",
    info1: "Trafo: 12345",
    info2: "Sayaç: 67890",
    description: "Kasım ayı tüketim bedeli",
    period: "2025-10",
    dueDate: "16.11.2025",
    currency: "TL",
    invoiceAmount: 1708.0,
    payableAmount: 1708.0
  },
  {
    key: 2,
    customerName: "AYDIN İNŞ. TİC. TURİZM VE SAN. LTD. ŞTİ.",
    invoiceNo: "000000002108",
    debtType: "Elektrik",
    info1: "Trafo: 12345",
    info2: "Sayaç: 67890",
    description: "Aralık ayı tüketim bedeli",
    period: "2025-12",
    dueDate: "01.05.2025",
    currency: "TL",
    invoiceAmount: 324.0,
    payableAmount: 324.0
  },
  {
    key: 3,
    customerName: "AYDIN İNŞ. TİC. TURİZM VE SAN. LTD. ŞTİ.",
    invoiceNo: "000000002109",
    debtType: "Elektrik",
    info1: "Trafo: 99881",
    info2: "Sayaç: 11223",
    description: "Gecikme farkı",
    period: "2025-12",
    dueDate: "10.05.2025",
    currency: "TL",
    invoiceAmount: 86.5,
    payableAmount: 86.5
  }
];

function formatCurrency(value) {
  return new Intl.NumberFormat("tr-TR", {
    style: "currency",
    currency: "TRY",
    minimumFractionDigits: 2
  }).format(value);
}

function getDynamicField(institution) {
  return (
    dynamicFieldConfig[institution] || {
      label: "Sorgu Bilgisi",
      placeholder: "Bilgi giriniz"
    }
  );
}
function BillPaymentTeller() {


  <>
  <div>asdsa</div>
    </>
 
}

export default BillPaymentTeller;
