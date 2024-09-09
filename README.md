const lang = {
  returnMapHead: {
    label: "Error Code Parameters",
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
  bankReturnText: {
    label: "Bank Error Description",
  },
  returnType: {
    label: "Return Type",
    options: {
      success: "Successful",
      error: "Failed",
    },
  },
  reversible: {
    label: "Reversible",
  },
  messages: {
    createSuccessMessage: "Addition Successful.",
    createErrorMessage: "Failed Addition!",
    createSelectDefinition: "Please select a return code!",
    createFullAlan: "Please fill in all fields!",
    createAlreadyExist: "Such data is already registered.",
    createMaxMın: "Return code must be at least 5 characters!",
    selectDefinitionValidatedMessage: "Please select a return code!",
    institutionReturnCodeValidated: "Only numbers are allowed in the Institution Error Code field!",
    bankReturnCodeValidated: "Only numbers are allowed in the Bank Error Code field!",
    updateSuccessMessage: "Update Successful.",
    updateErrorMessage: "Failed Update!",
    updatedAlreadyExist: "Failed update! Such a record already exists.",
    deleteTitle: "Delete Record",
    deleteContent: "Are you sure you want to delete the record?",
    deleteSuccess: "Delete Successful.",
    deleteError: "Failed deletion process!",
    performOnlyPackageGroupWithOfflineInquiryTypeWarningMessage:
      "Only actions for the Package Group with OFFLINE Inquiry Type can be performed.",
  },
  dataListed: "Data is being listed...",
  closeButton: "CLOSE",
  cancelButton: "CANCEL",
  submitButton: "SAVE",
  okButton: "OK",
};

export default lang;
