const data = [
  {
    key: '1',
    name: 'John Brown',
    age: 32,
    address: 'New York No. 1 Lake Park',
  },
  {
    key: '2',
    name: 'Jim Green',
    age: 42,
    address: 'London No. 1 Lake Park',
  },
  {
    key: '3',
    name: 'Joe Black',
    age: 32,
    address: 'Sidney No. 1 Lake Park',
  },
  {
    key: '4',
    name: 'Jim Red',
    age: 32,
    address: 'London No. 2 Lake Park',
  },
];

const App = () => {
  const [sortedInfo, setSortedInfo] = useState({});
  const [filteredInfo, setFilteredInfo] = useState({});

  const handleChange = (pagination, filters, sorter) => {
    console.log('Various parameters', pagination, filters, sorter);
    setSortedInfo(sorter);
    setFilteredInfo(filters);
  };

  const clearFilters = () => {
    setFilteredInfo({});
  };

  const clearAll = () => {
    setFilteredInfo({});
    setSortedInfo({});
  };

  const setAgeSort = () => {
    setSortedInfo({ order: 'descend', columnKey: 'age' });
  };

  const columns = [
    {
      title: 'Name',
      dataIndex: 'name',
      key: 'name',
      filters: [
        { text: 'Joe', value: 'Joe' },
        { text: 'Jim', value: 'Jim' },
      ],
      autoFilter: true,
      multiple: true,
      filteredValue: filteredInfo.name || null,
      onFilter: (value, record) => record.name.includes(value),
      sorter: (a, b) => a.name.length - b.name.length,
      sortOrder: sortedInfo.columnKey === 'name' && sortedInfo.order,
    },
    {
      title: 'Age',
      dataIndex: 'age',
      key: 'age',
      sorter: (a, b) => a.age - b.age,
      sortOrder: sortedInfo.columnKey === 'age' && sortedInfo.order,
    },
    {
      title: 'Address',
      dataIndex: 'address',
      key: 'address',
      filters: [
        { text: 'London', value: 'London' },
        { text: 'New York', value: 'New York' },
      ],
      autoFilter: true,
      multiple: true,
      filteredValue: filteredInfo.address || null,
      onFilter: (value, record) => record.address.includes(value),
      sorter: (a, b) => a.address.length - b.address.length,
      sortOrder: sortedInfo.columnKey === 'address' && sortedInfo.order,
    },
  ];

  return (
    <div>
      <div style={{ marginBottom: '10px' }}>
        <Button onClick={setAgeSort}>Sort age</Button>
        <Button onClick={clearFilters}>Clear filters</Button>
        <Button onClick={clearAll}>Clear filters and sorters</Button>
      </div>
      <Table columns={columns} data={data} onChange={handleChange} />
    </div>
  );
};
<App />;



      <Table columns={columns} data={dataWithKeys} loading={{ text: 'Veriler listeleniyor...', status: spinnig }} />

const columns = [
    {
      title: 'Dönüş Kodu',
      dataIndex: 'returnMapCode',
      key: 'returnMapCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Kurum Kodu',
      dataIndex: 'institutionReturnCode',
      key: 'institutionReturnCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Kurum Metni',
      dataIndex: 'institutionReturnText',
      key: 'institutionReturnText',
      width: 200,
      resizable: true,
    },
    {
      title: 'Banka Kodu',
      dataIndex: 'bankReturnCode',
      key: 'bankReturnCode',
      width: 200,
      resizable: true,
    },
    {
      title: 'Banka Metni',
      dataIndex: 'bankReturnText',
      key: 'bankReturnText',
      width: 200,
      resizable: true,
    },
    {
      title: 'Dönüş Tipi',
      dataIndex: 'returnType',
      key: 'returnType',
      width: 200,
      resizable: true,
    },
    {
      title: 'Geri Dönüş',
      dataIndex: 'isReversible',
      key: 'isReversible',
      width: 200,
      render: value => (value ? 'Evet' : 'Hayır'),
      resizable: true,
    },
    {
      key: "edit",
      width: 50,
      align: "center",
      render: (text, record) => (
        <SecureButton
          type="primary"
          size="small"
          title={"Güncelle"}
          permission="handleEdit"
          onClick={() => handleEdit(record)}
        >
          <Icon name="edit" size="small" />
        </SecureButton>
      ),
    },
    {
      key: 'actions',
      width: 100,
      render: (text, record) => (
        <DeleteButton
          type="danger"
          size="small"
          title={"Sil"}
          permission="handleClickOneDelete"
          onClick={() => {
            handleClickOneDelete(record);
          }}
        >
          <Icon name="trash" size="small" />
        </DeleteButton>
      )
    }
  ];
