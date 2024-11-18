private boolean validateRequiredColumns(IBasicFormActions controller) {
    if (controller instanceof AbstractListViewController) {
        AbstractListViewController listController = (AbstractListViewController) controller;
        return listController.hasColumn("DOVIZ") &&
               listController.hasColumn("ISLEMTUTAR") &&
               listController.hasColumn("KKMASRAF") &&
               listController.hasColumn("BSMV") &&
               listController.hasColumn("TOPLAMTUTAR");
    }
    return false;
}
