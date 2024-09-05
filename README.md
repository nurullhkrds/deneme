const columns = [
  {
    title: 'Dönüş Kodu',
    dataIndex: 'returnMapCode',
    key: 'returnMapCode',
    width: 200,
    resizable: true,
    sorter: (a, b) => a.returnMapCode.localeCompare(b.returnMapCode),
  },
  {
    title: 'Aktif',
    dataIndex: 'isActive',
    key: 'isActive',
    width: 200,
    render: value => (value ? 'Evet' : 'Hayır'),
    resizable: true,
    sorter: (a, b) => a.isActive - b.isActive,
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
        permission="handleDefinitionEdit"
        onClick={() => handleDefinitionEdit(record)}
      >
        <Icon name="edit" size="small" />
      </SecureButton>
    ),
  },
  {
    key: 'actions',
    width: 50,
    align: "center",
    render: (text, record) => (
      <DeleteButton
        type="danger"
        size="small"
        title={"Sil"}
        permission="handleClickOneDefinitionDelete"
        onClick={() => {
          handleClickOneDefinitionDelete(record);
        }}
      >
        <Icon name="trash" size="small" />
      </DeleteButton>
    ),
  }
];
