  {
    key: "edit",
    width: 50,
    align: "center",
    render: (text, record) =>
      record.isActive ? (
        <SecureButton
          type="primary"
          size="small"
          title={"Güncelle"}
          permission="handleEdit"
          onClick={() => handleEdit(record)}
        >
          <Icon name="edit" size="small" />
        </SecureButton>
      ) : null, // Do not render the button if isActive is false
  },
