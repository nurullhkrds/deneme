const lang = {
  returnMapHead: {
    label: "Error Codes Parameters",
    labelSecond: "Error Codes Definition Parameters"
  },
  returnMap: {
    label: "Return Code",
  },
  institutionReturnCode: {
    label: "Institution Error Code",
  },
  institutionReturnText: {
    label: "Institution Error Description",
  },
  bankReturnCode: {
    label: "Bank Error Code",
  },

  bankRetubankReturnTextrnCode: {
    label: "Bank Error Description",
  },
  returnType: {
    label: "Return Type",
    options: {
      success: "Successful",
      error: "Failed",
    },
  },

  isActive: {
    label: "Status"
  },

  reversible: {
    label: "Reversible"
  },

  messages: {
    createSuccessMessage: "Addition Successful.",
    createErrorMessage: 'Failed to Add!',
    createSelectDefinition: 'Please select a return code!',
    createFullAlan: "Please fill in all fields!",
    createAlreadyExist: "This data already exists.",
    createMaxMın: "Return code must be at least 5 characters!",
    selectDefinitionValidatedMessage: "Please select a return code!",
    institutionReturnCodeValidated: "Only numbers are allowed in the Institution Error Code field!",
    bankReturnCodeValidated: "Only numbers are allowed in the Bank Error Code field!",

    updateSuccessMessage: "Update Successful",
    updateErrorMessage: "Failed to Update!",
    updatedAlreadyExist: "Failed to update! This record already exists.",

    deleteTitle: "Delete Record",
    deleteContent: "Are you sure you want to delete this record?",
    deleteSuccess: "Delete Successful",
    deleteError: "Failed to Delete!",

    performOnlyPackageGroupWithOfflineInquiryTypeWarningMessage:
      "Operations can only be performed for the Package Group with OFFLINE Inquiry Type",
  },

  dataListed: "Data is being listed...",

  closeButton: "CLOSE",
  cancelButton: "CANCEL",
  submitButton: "SAVE",
  okButton: "OK",
  createButton: "ADD",
  deleteButton: "YES",
  clearButton: "CLEAR",
  searchButton: "LIST"
};

export default lang;
