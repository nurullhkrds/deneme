if (!requiredValues.isEmpty()) {
    String missingFieldsMessage = "Dekont tanımındaki zorunlu alanlardan " + 
        String.join(", ", requiredValues) + 
        (requiredValues.size() == 1 ? " alanı" : " alanları") + 
        " eksiktir.";
    MessagesUtil.addError(missingFieldsMessage, events);
}
