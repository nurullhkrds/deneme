const columns = [
  {
    title: 'Return Map Code',
    dataIndex: 'returnMapCode',
    key: 'returnMapCode',
    width: 200,
    resizable: true,
  },
  {
    title: 'Institution Return Code',
    dataIndex: 'institutionReturnCode',
    key: 'institutionReturnCode',
    width: 200,
    resizable: true,
  },
  {
    title: 'Institution Return Text',
    dataIndex: 'institutionReturnText',
    key: 'institutionReturnText',
    width: 200,
    resizable: true,
  },
  {
    title: 'Bank Return Code',
    dataIndex: 'bankReturnCode',
    key: 'bankReturnCode',
    width: 200,
    resizable: true,
  },
  {
    title: 'Bank Return Text',
    dataIndex: 'bankReturnText',
    key: 'bankReturnText',
    width: 200,
    resizable: true,
  },
  {
    title: 'Return Type',
    dataIndex: 'returnType',
    key: 'returnType',
    width: 200,
    resizable: true,
  },
  {
    title: 'Is Reversible',
    dataIndex: 'isReversible',
    key: 'isReversible',
    width: 200,
    render: value => (value ? 'Evet' : 'Hayır'),
    resizable: true,
  },
  {
    title: 'Actions',
    key: 'actions',
    width: 100,
    render: (text, record) => (
      <Button onClick={() => handleEdit(record)}>Edit</Button>
    ),
  },
];

const ReturnMapServiceParametersTable = () => {
  const returnMapData = useSelector((state) => state.returnMap.list);
  const dispatch = useDispatch();

  const dataWithKeys = useMemo(
    () => returnMapData.map((item, index) => ({ ...item, key: index + 1 })),
    [returnMapData]
  );

  const rowSelection = {
    onChange: (selectedRowKeys, selectedRows) => {
      console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows);
      const selectedIds = selectedRows.map(row => row.id);
      dispatch(setSelectedReturnMapList(selectedIds));
    },
    getCheckboxProps: record => ({
      disabled: record.name === 'Disabled User',
      name: record.name,
    }),
  };

  const handleEdit = (record) => {
    // Your edit logic here
    console.log('Editing record:', record);
  };

  return (
    <Table rowSelection={rowSelection} columns={columns} data={dataWithKeys} />
  );
};

export default ReturnMapServiceParametersTable;
