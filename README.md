import React, { useMemo, useState } from "react";
import {
  Alert,
  Button,
  Card,
  Col,
  Input,
  Layout,
  Row,
  Select,
  Space,
  Table,
  Typography,
  theme
} from "antd";
import {
  BellOutlined,
  QuestionCircleOutlined,
  SearchOutlined
} from "@ant-design/icons";

const { Header, Content } = Layout;
const { Title, Text } = Typography;
const { Option } = Select;

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

export default function BillPaymentCorporateAntdPage() {
  const { token } = theme.useToken();

  const [filters, setFilters] = useState({
    quickInstitutionSearch: "",
    city: "İstanbul",
    product: "Tahsilat",
    debtType: "Fatura",
    institution: "Enerjisa Müşteri Çözümleri",
    serviceType: "Seçiniz",
    dynamicValue: ""
  });

  const [showResults, setShowResults] = useState(true);
  const [tableSearch, setTableSearch] = useState("");
  const [selectedRowKeys, setSelectedRowKeys] = useState([1, 2]);

  const dynamicField = useMemo(
    () => getDynamicField(filters.institution),
    [filters.institution]
  );

  const filteredBills = useMemo(() => {
    const q = tableSearch.trim().toLocaleLowerCase("tr");
    if (!q) return initialBills;

    return initialBills.filter((item) =>
      [
        item.customerName,
        item.invoiceNo,
        item.debtType,
        item.info1,
        item.info2,
        item.description,
        item.period,
        item.dueDate,
        item.currency
      ]
        .join(" ")
        .toLocaleLowerCase("tr")
        .includes(q)
    );
  }, [tableSearch]);

  const selectedBills = filteredBills.filter((item) =>
    selectedRowKeys.includes(item.key)
  );

  const totalAmount = selectedBills.reduce(
    (sum, item) => sum + item.payableAmount,
    0
  );

  const handleFilterChange = (key, value) => {
    setFilters((prev) => ({
      ...prev,
      [key]: value
    }));
  };

  const handleTemizle = () => {
    setFilters({
      quickInstitutionSearch: "",
      city: "İstanbul",
      product: "Tahsilat",
      debtType: "Fatura",
      institution: "Enerjisa Müşteri Çözümleri",
      serviceType: "Seçiniz",
      dynamicValue: ""
    });
    setTableSearch("");
    setShowResults(false);
    setSelectedRowKeys([1, 2]);
  };

  const columns = [
    {
      title: "Ad Soyad / Unvan",
      dataIndex: "customerName",
      key: "customerName",
      width: 280,
      fixed: "left",
      ellipsis: true
    },
    {
      title: "Fatura No",
      dataIndex: "invoiceNo",
      key: "invoiceNo",
      width: 170
    },
    {
      title: "Borç Türü",
      dataIndex: "debtType",
      key: "debtType",
      width: 140
    },
    {
      title: "Info1",
      dataIndex: "info1",
      key: "info1",
      width: 160,
      ellipsis: true
    },
    {
      title: "Info2",
      dataIndex: "info2",
      key: "info2",
      width: 160,
      ellipsis: true
    },
    {
      title: "Açıklama",
      dataIndex: "description",
      key: "description",
      width: 240,
      ellipsis: true
    },
    {
      title: "Dönem",
      dataIndex: "period",
      key: "period",
      width: 130
    },
    {
      title: "Son Ödeme Tarihi",
      dataIndex: "dueDate",
      key: "dueDate",
      width: 160
    },
    {
      title: "Döviz",
      dataIndex: "currency",
      key: "currency",
      width: 100
    },
    {
      title: "Fatura Tutarı",
      dataIndex: "invoiceAmount",
      key: "invoiceAmount",
      width: 170,
      align: "right",
      render: (value) => <Text strong>{formatCurrency(value)}</Text>
    },
    {
      title: "Ödenecek Tutar",
      dataIndex: "payableAmount",
      key: "payableAmount",
      width: 180,
      align: "right",
      render: (value) => <Text strong>{formatCurrency(value)}</Text>
    }
  ];

  const rowSelection = {
    selectedRowKeys,
    onChange: setSelectedRowKeys
  };

  return (
    <Layout
      style={{
        minHeight: "100vh",
        background: "#f5f7fb"
      }}
    >
      <Header
        style={{
          background: "#fff",
          padding: "0 24px",
          borderBottom: "1px solid #e8edf5",
          display: "flex",
          alignItems: "center",
          justifyContent: "space-between"
        }}
      >
        <Space size={12}>
          <div
            style={{
              width: 36,
              height: 36,
              borderRadius: 10,
              background: "#eaf2ff",
              display: "grid",
              placeItems: "center",
              color: token.colorPrimary,
              fontWeight: 700
            }}
          >
            ▦
          </div>
          <Title level={3} style={{ margin: 0 }}>
            Fatura Ödeme
          </Title>
        </Space>

        <Space>
          <Button icon={<QuestionCircleOutlined />}>Yardım</Button>
          <Button icon={<BellOutlined />}>Bildirimler</Button>
          <Button type="primary" shape="circle">
            AO
          </Button>
        </Space>
      </Header>

      <Content style={{ padding: 24 }}>
        <Space direction="vertical" size={16} style={{ width: "100%" }}>
          <Card title="Kurum ve Sorgulama Bilgileri">
            <Row gutter={[16, 16]}>
              <Col xs={24} sm={12} md={8} lg={6}>
                <Text strong>Hızlı Kurum Arama</Text>
                <Input
                  style={{ marginTop: 8 }}
                  placeholder="Kurum adı ile ara"
                  prefix={<SearchOutlined />}
                  value={filters.quickInstitutionSearch}
                  onChange={(e) =>
                    handleFilterChange("quickInstitutionSearch", e.target.value)
                  }
                />
              </Col>

              <Col xs={24} sm={12} md={8} lg={4}>
                <Text strong>Şehir</Text>
                <Select
                  style={{ width: "100%", marginTop: 8 }}
                  value={filters.city}
                  onChange={(value) => handleFilterChange("city", value)}
                >
                  {cityOptions.map((item) => (
                    <Option key={item} value={item}>
                      {item}
                    </Option>
                  ))}
                </Select>
              </Col>

              <Col xs={24} sm={12} md={8} lg={4}>
                <Text strong>Ürün</Text>
                <Select
                  style={{ width: "100%", marginTop: 8 }}
                  value={filters.product}
                  onChange={(value) => handleFilterChange("product", value)}
                >
                  {productOptions.map((item) => (
                    <Option key={item} value={item}>
                      {item}
                    </Option>
                  ))}
                </Select>
              </Col>

              <Col xs={24} sm={12} md={8} lg={4}>
                <Text strong>Borç Türü</Text>
                <Select
                  style={{ width: "100%", marginTop: 8 }}
                  value={filters.debtType}
                  onChange={(value) => handleFilterChange("debtType", value)}
                >
                  {debtTypeOptions.map((item) => (
                    <Option key={item} value={item}>
                      {item}
                    </Option>
                  ))}
                </Select>
              </Col>

              <Col xs={24} sm={12} md={8} lg={6}>
                <Text strong>Kurum</Text>
                <Select
                  style={{ width: "100%", marginTop: 8 }}
                  value={filters.institution}
                  onChange={(value) => handleFilterChange("institution", value)}
                  showSearch
                  optionFilterProp="children"
                >
                  {institutionOptions.map((item) => (
                    <Option key={item} value={item}>
                      {item}
                    </Option>
                  ))}
                </Select>
              </Col>

              <Col xs={24} sm={12} md={8} lg={6}>
                <Text strong>{dynamicField.label}</Text>
                <Input
                  style={{ marginTop: 8 }}
                  placeholder={dynamicField.placeholder}
                  value={filters.dynamicValue}
                  onChange={(e) =>
                    handleFilterChange("dynamicValue", e.target.value)
                  }
                />
              </Col>

              <Col xs={24} sm={12} md={8} lg={4}>
                <Text strong>Servis Tipi</Text>
                <Select
                  style={{ width: "100%", marginTop: 8 }}
                  value={filters.serviceType}
                  onChange={(value) => handleFilterChange("serviceType", value)}
                >
                  {serviceTypeOptions.map((item) => (
                    <Option key={item} value={item}>
                      {item}
                    </Option>
                  ))}
                </Select>
              </Col>

              <Col
                xs={24}
                lg={14}
                style={{
                  display: "flex",
                  alignItems: "end",
                  justifyContent: "flex-end"
                }}
              >
                <Space wrap>
                  <Button onClick={handleTemizle}>Temizle</Button>
                  <Button onClick={() => setShowResults(false)}>Getir</Button>
                  <Button type="primary" onClick={() => setShowResults(true)}>
                    Sorgula
                  </Button>
                </Space>
              </Col>
            </Row>
          </Card>

          {showResults && (
            <>
              <Alert
                type="success"
                showIcon
                message="Borç bilgileri başarıyla getirildi."
                description="Lütfen ödemek istediğiniz faturaları seçin."
              />

              <Card>
                <Row
                  gutter={[16, 16]}
                  justify="space-between"
                  align="middle"
                  style={{ marginBottom: 16 }}
                >
                  <Col>
                    <Space wrap>
                      <Button>Toplu İşlemler</Button>
                    </Space>
                  </Col>

                  <Col>
                    <Space wrap>
                      <Input
                        placeholder="Tabloda ara"
                        prefix={<SearchOutlined />}
                        value={tableSearch}
                        onChange={(e) => setTableSearch(e.target.value)}
                        style={{ width: 240 }}
                      />
                      <Button>Kolonlar</Button>
                    </Space>
                  </Col>
                </Row>

                <Table
                  rowSelection={rowSelection}
                  columns={columns}
                  dataSource={filteredBills}
                  pagination={{
                    pageSize: 10,
                    showSizeChanger: true,
                    pageSizeOptions: ["10", "25", "50"]
                  }}
                  scroll={{ x: 1900 }}
                  bordered
                  size="middle"
                />

                <Row
                  justify="space-between"
                  align="middle"
                  gutter={[16, 16]}
                  style={{ marginTop: 16 }}
                >
                  <Col>
                    <Text strong>{selectedRowKeys.length} adet seçili</Text>
                  </Col>

                  <Col>
                    <Space wrap size="large">
                      <Text>
                        Toplam Tutar:{" "}
                        <Text strong style={{ fontSize: 18 }}>
                          {formatCurrency(totalAmount)}
                        </Text>
                      </Text>
                      <Button>Geri</Button>
                      <Button type="primary">Ödemeye Devam</Button>
                    </Space>
                  </Col>
                </Row>
              </Card>
            </>
          )}
        </Space>
      </Content>
    </Layout>
  );
}
