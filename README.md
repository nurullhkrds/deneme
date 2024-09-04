const returnMapSlice = createSlice({
  name: 'returnMap',
  initialState: {
    list: [],
    spinning: false,
    error: null,


    selectedList: [],
    selectedSpinning: false,
    selectedError: null,

    selectedRowKeys: [],


    returnMapData: null,
    returnMapDataSpinning: false,
    returnMapDataError: null,






    returnMapDefinition: null,
    returnMapDefinitionSpinning: false,
    returnMapDefinitionError: null,


    returnMapDefinitionList: [],
    returnMapDefinitionListSpinnig: false,
    returnMapDefinitionListError: null,




    returnMapCode: null,
    returnMapCodeSpinnig: false,
    returnMapCodeError: null,

    subTableActive: false,


  },
  reducers: {
    setReturnMapList: (state, action) => {
      state.list = action.payload;
    },
    setSpinning: (state, action) => {
      state.spinning = action.payload;
    },
    setError: (state, action) => {
      state.error = action.payload;
    },


    setReturnMapCode: (state, action) => {
      state.returnMapCode = action.payload;
    },
    setReturnMapCodeSpinnig: (state, action) => {
      state.returnMapCodeSpinnig = action.payload;
    },
    setReturnMapCodeError: (state, action) => {
      state.returnMapCodeError = action.payload;
    },


    setSelectedReturnMapList: (state, action) => {
      state.selectedList = action.payload;
    },
    setSelectedSpinning: (state, action) => {
      state.selectedSpinning = action.payload;
    },
    setSelectedError: (state, action) => {
      state.selectedError = action.payload
    },
    setSelectedReturnMapListReset: (state, action) => {
      state.selectedList = [];
    },


    resetSelectedRowKeys(state) {
      state.selectedRowKeys = [];
    },
    setSelectedRowKeys(state, action) {
      state.selectedRowKeys = action.payload;
    },



    setreturnMapData: (state, action) => {
      state.returnMapData = action.payload;
    },
    setreturnMapDataSpinning: (state, action) => {
      state.returnMapDataSpinning = action.payload;
    },
    setreturnMapDataError: (state, action) => {
      state.returnMapDataError = action.payload
    },




    //definition için bir değişken
    setReturnMapDefinitionData: (state, action) => {
      state.returnMapDefinition = action.payload;
    },
    setReturnMapDefinitionDataSpinning: (state, action) => {
      state.returnMapDefinitionSpinning = action.payload;
    },
    setReturnMapDefinitionDataError: (state, action) => {
      state.returnMapDefinitionError = action.payload
    },




    setRetunMapDefinitionList: (state, action) => {
      state.returnMapDefinitionList = action.payload;
    },
    setReturnMapDefinitionListSpinning: (state, action) => {
      state.returnMapDefinitionListSpinnig = action.payload;
    },
    setReturnMapDefinitionListError: (state, action) => {
      state.returnMapDefinitionListError = action.payload;
    },

  },
});
