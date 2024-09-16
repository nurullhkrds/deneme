      <SecureButton
          type="primary"
          size="small"
          title={"Güncelle"}
          permission="handleEdit"
          onClick={() => handleEdit(record)}
        >
          <Icon name="edit" size="small" />
          {returnMapOneData.returnMapDefinition.isActive && 'disabled'}
        </SecureButton>
