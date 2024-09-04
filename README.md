reducers: {
    setReturnMapCode: (state, action) => {
      state.returnMapCode = action.payload;
    },
    setReturnMapCodeSpinning: (state, action) => {
      state.returnMapCodeSpinning = action.payload;
    },
    setReturnMapCodeError: (state, action) => {
      state.returnMapCodeError = action.payload;
    },
    toggleSubTableActive: (state, action) => {
      state.subTableActive = action.payload;  // Gelen true/false değerini set etme
    },
  },



setReturnMapCode, setReturnMapCodeSpinning, setReturnMapCodeError, toggleSubTableActive 
