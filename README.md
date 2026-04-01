import React, { useMemo, useState } from "react";
import {
  Card,
  Row,
  Col,
  Button,
  Form,
  Select,
  Checkbox,
  Icon,
  Layout,
  Header,
  Space,
  Title,
  TextInput,
  InfoBox,
  Table
} from "ykb-ui";

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
  }).format(value || 0);
}

function getDynamicField(institution) {
  return (
    dynamicFieldConfig[institution] || {
      label: "Sorgu Bilgisi",
      placeholder: "Bilgi giriniz"
    }
  );
}

function FieldBlock({ label, children }) {
  return (
    <div style={{ display: "flex", flexDirection: "column", gap: 8, width: "100%" }}>
      <label style={{ fontSize: 13, fontWeight: 600, color: "#475467" }}>{label}</label>
      {children}
    </div>
  );
}

function BillPaymentTeller() {
  const [filters, setFilters] = useState({
    quickInstitutionSearch: "",
    city: "İstanbul",
    product: "Tahsilat",
    debtType: "Fatura",
    institution: "Enerjisa Müşteri Çözümleri",
    serviceType: "Seçiniz",
    dynamicValue: ""
  });

  const [tableSearch, setTableSearch] = useState("");
  const [showResults, setShowResults] = useState(true);
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

  const handleFilterChange = (field, value) => {
    setFilters((prev) => ({
      ...prev,
      [field]: value
    }));
  };

  const handleClear = () => {
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
    setSelectedRowKeys([1, 2]);
    setShowResults(false);
  };

  const rowSelection = {
    selectedRowKeys,
    onChange: (keys) => setSelectedRowKeys(keys)
  };

  const columns = [
    {
      title: "Ad Soyad / Unvan",
      dataIndex: "customerName",
      key: "customerName",
      width: 280
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
      width: 160
    },
    {
      title: "Info2",
      dataIndex: "info2",
      key: "info2",
      width: 160
    },
    {
      title: "Açıklama",
      dataIndex: "description",
      key: "description",
      width: 240
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
      render: (value) => formatCurrency(value)
    },
    {
      title: "Ödenecek Tutar",
      dataIndex: "payableAmount",
      key: "payableAmount",
      width: 180,
      align: "right",
      render: (value) => formatCurrency(value)
    }
  ];

  return (
    <Layout style={{ minHeight: "100vh", background: "#F5F7FA" }}>
      <Header
        style={{
          background: "#FFFFFF",
          borderBottom: "1px solid #E4E7EC",
          padding: "16px 24px"
        }}
      >
        <Row justify="space-between" align="middle">
          <Col>
            <Space align="center">
              <div
                style={{
                  width: 36,
                  height: 36,
                  borderRadius: 10,
                  background: "#EEF4FF",
                  display: "flex",
                  alignItems: "center",
                  justifyContent: "center"
                }}
              >
                <Icon name="wallet" />
              </div>
              <Title level={4} style={{ margin: 0 }}>
                Fatura Ödeme
              </Title>
            </Space>
          </Col>

          <Col>
            <Space>
              <Button type="default">Yardım</Button>
              <Button type="default">Bildirimler</Button>
            </Space>
          </Col>
        </Row>
      </Header>

      <div style={{ padding: 24 }}>
        <Space direction="vertical" size={16} style={{ width: "100%" }}>
          <Card title="Kurum ve Sorgulama Bilgileri">
            <Form>
              <Row gutter={16}>
                <Col span={6}>
                  <FieldBlock label="Hızlı Kurum Arama">
                    <TextInput
                      placeholder="Kurum adı ile ara"
                      value={filters.quickInstitutionSearch}
                      onChange={(e) =>
                        handleFilterChange(
                          "quickInstitutionSearch",
                          e?.target ? e.target.value : e
                        )
                      }
                    />
                  </FieldBlock>
                </Col>

                <Col span={3}>
                  <FieldBlock label="Şehir">
                    <Select
                      value={filters.city}
                      onChange={(value) => handleFilterChange("city", value)}
                      options={cityOptions.map((item) => ({
                        label: item,
                        value: item
                      }))}
                    />
                  </FieldBlock>
                </Col>

                <Col span={3}>
                  <FieldBlock label="Ürün">
                    <Select
                      value={filters.product}
                      onChange={(value) => handleFilterChange("product", value)}
                      options={productOptions.map((item) => ({
                        label: item,
                        value: item
                      }))}
                    />
                  </FieldBlock>
                </Col>

                <Col span={3}>
                  <FieldBlock label="Borç Türü">
                    <Select
                      value={filters.debtType}
                      onChange={(value) => handleFilterChange("debtType", value)}
                      options={debtTypeOptions.map((item) => ({
                        label: item,
                        value: item
                      }))}
                    />
                  </FieldBlock>
                </Col>

                <Col span={5}>
                  <FieldBlock label="Kurum">
                    <Select
                      value={filters.institution}
                      onChange={(value) => handleFilterChange("institution", value)}
                      options={institutionOptions.map((item) => ({
                        label: item,
                        value: item
                      }))}
                    />
                  </FieldBlock>
                </Col>

                <Col span={4}>
                  <FieldBlock label="Servis Tipi">
                    <Select
                      value={filters.serviceType}
                      onChange={(value) => handleFilterChange("serviceType", value)}
                      options={serviceTypeOptions.map((item) => ({
                        label: item,
                        value: item
                      }))}
                    />
                  </FieldBlock>
                </Col>
              </Row>

              <Row gutter={16} style={{ marginTop: 16 }}>
                <Col span={10}>
                  <FieldBlock label={dynamicField.label}>
                    <TextInput
                      placeholder={dynamicField.placeholder}
                      value={filters.dynamicValue}
                      onChange={(e) =>
                        handleFilterChange(
                          "dynamicValue",
                          e?.target ? e.target.value : e
                        )
                      }
                    />
                  </FieldBlock>
                </Col>

                <Col span={14}>
                  <div
                    style={{
                      display: "flex",
                      justifyContent: "flex-end",
                      alignItems: "flex-end",
                      height: "100%",
                      gap: 8
                    }}
                  >
                    <Button type="default" onClick={handleClear}>
                      Temizle
                    </Button>
                    <Button type="default" onClick={() => setShowResults(false)}>
                      Getir
                    </Button>
                    <Button type="primary" onClick={() => setShowResults(true)}>
                      Sorgula
                    </Button>
                  </div>
                </Col>
              </Row>
            </Form>
          </Card>

          {showResults && (
            <>
              <InfoBox
                status="success"
                title="Borç bilgileri başarıyla getirildi."
                description="Lütfen ödemek istediğiniz faturaları seçin."
              />

              <Card
                title="Borç Listesi"
                extra={
                  <Space>
                    <TextInput
                      placeholder="Tabloda ara"
                      value={tableSearch}
                      onChange={(e) =>
                        setTableSearch(e?.target ? e.target.value : e)
                      }
                      style={{ width: 240 }}
                    />
                    <Button type="default">Kolonlar</Button>
                  </Space>
                }
              >
                <Table
                  columns={columns}
                  dataSource={filteredBills}
                  rowSelection={rowSelection}
                  pagination={{
                    pageSize: 10
                  }}
                  scroll={{ x: 1900 }}
                />
              </Card>

              <Card>
                <Row justify="space-between" align="middle">
                  <Col>
                    <Space>
                      <Checkbox
                        checked={
                          filteredBills.length > 0 &&
                          filteredBills.every((item) =>
                            selectedRowKeys.includes(item.key)
                          )
                        }
                        onChange={(e) => {
                          const checked = e?.target ? e.target.checked : e;
                          if (checked) {
                            setSelectedRowKeys(filteredBills.map((item) => item.key));
                          } else {
                            setSelectedRowKeys([]);
                          }
                        }}
                      />
                      <span>{selectedRowKeys.length} adet seçili</span>
                    </Space>
                  </Col>

                  <Col>
                    <Space>
                      <span>Toplam Tutar:</span>
                      <strong style={{ fontSize: 18 }}>
                        {formatCurrency(totalAmount)}
                      </strong>
                      <Button type="default">Geri</Button>
                      <Button type="primary">Ödemeye Devam</Button>
                    </Space>
                  </Col>
                </Row>
              </Card>
            </>
          )}
        </Space>
      </div>
    </Layout>
  );
}

export default BillPaymentTeller;
