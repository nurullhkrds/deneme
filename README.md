const columns = [
  {
    title: ReturnMapFormLocale.returnMap.label,
    dataIndex: 'returnMapCode',
    key: 'returnMapCode',
    width: 200,
    resizable: true,
    sorter: (a, b) => a.returnMapCode.localeCompare(b.returnMapCode),
  },
  {
    title: ReturnMapFormLocale.institutionReturnCode.label,
    dataIndex: 'institutionReturnCode',
    key: 'institutionReturnCode',
    width: 200,
    resizable: true,
    sorter: (a, b) => a.institutionReturnCode.localeCompare(b.institutionReturnCode),
  },
  {
    title: ReturnMapFormLocale.institutionReturnText.label,
    dataIndex: 'institutionReturnText',
    key: 'institutionReturnText',
    width: 200,
    resizable: true,
    sorter: (a, b) => a.institutionReturnText.localeCompare(b.institutionReturnText),
  },
  {
    title: ReturnMapFormLocale.bankReturnCode.label,
    dataIndex: 'bankReturnCode',
    key: 'bankReturnCode',
    width: 200,
    resizable: true,
    sorter: (a, b) => a.bankReturnCode.localeCompare(b.bankReturnCode),
  },
  {
    title: ReturnMapFormLocale.bankReturnText.label,
    dataIndex: 'bankReturnText',
    key: 'bankReturnText',
    width: 200,
    resizable: true,
    sorter: (a, b) => a.bankReturnText.localeCompare(b.bankReturnText),
  },
  {
    title: ReturnMapFormLocale.returnType.label,
    dataIndex: 'returnType',
    key: 'returnType',
    width: 200,
    resizable: true,
    sorter: (a, b) => a.returnType.localeCompare(b.returnType),
    render: value => (value === 'SUCCESS' ? ReturnMapFormLocale.returnType.options.success : ReturnMapFormLocale.returnType.options.error),
  },
  {
    title: ReturnMapFormLocale.reversible.label,
    dataIndex: 'isReversible',
    key: 'isReversible',
    width: 200,
    render: value => (value ? 'Evet' : 'Hayır'),
    resizable: true,
    sorter: (a, b) => a.isReversible - b.isReversible,
  },
  ...(definition.isActive
    ? [
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
          ),
        },
      ]
    : [])
];
