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
