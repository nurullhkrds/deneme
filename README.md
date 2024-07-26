  const selectedRowKeys = useSelector((state) => state.returnMap.selectedRowKeys);
const rowSelection = {
    selectedRowKeys,
    onChange: (selectedRowKeys, selectedRows) => {
      console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows);
      const selectedIds = selectedRows.map(row => row.id);
      dispatch(setSelectedReturnMapList(selectedIds));
      dispatch(setSelectedRowKeys(selectedRowKeys));
    },
    getCheckboxProps: record => ({
      disabled: record.name === 'Disabled User',
      name: record.name,
    }),
  };


  const handleOk2 = () => {
    setModalVisible2(false);

    sendCopyReturnMapRequest(callApi, copyRequest)
      .then(() => {
        dispatch(fetchInstitutionsData(dispatch, callApi, returnMapCode2));
        dispatch(resetSelectedRowKeys()); // selectedRowKeys'i sıfırla
        Notification.success('Kopyalama Başarılı', 3);
      })
      .catch(error => {
        console.error('Error creating return map:', error);
        Notification.error('Hatalı Kopyalama', 3);
      });

    setReturnMapCode2("");
  };
