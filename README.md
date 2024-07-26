  selectedRowKeys: [],
};

const returnMapServiceParameterSlice = createSlice({
  name: 'returnMap',
  initialState,
  reducers: {
    setSelectedReturnMapList(state, action) {
      state.selectedList = action.payload;
    },
    // Yeni aksiyon
    resetSelectedRowKeys(state) {
      state.selectedRowKeys = [];
    },
    setSelectedRowKeys(state, action) {
      state.selectedRowKeys = action.payload;
    },
  },
});
