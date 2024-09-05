@Override
public Result deleteReturnMapDefinitions(DeleteIdsRequest request) {
    try {
        // Id'lere göre ReturnMapDefinition nesnelerini bul
        List<ReturnMapDefinition> returnMapsToDelete = returnMapDefinitionRepository.findAllById(request.getIds());

        // Her bir nesnenin isActive alanını false yap
        returnMapsToDelete.forEach(returnMap -> returnMap.setIsActive(false));

        // Değişiklikleri kaydet
        returnMapDefinitionRepository.saveAll(returnMapsToDelete);

        // Başarılı sonucu döndür
        return new SuccessResult(ResultConstant.SUCCESSFULLY_DELETED.getMessage(), 200);
    } catch (Exception e) {
        // Hata durumunda hata sonucu döndür
        return new ErrorResult("Error ! " + e.getMessage(), 400);
    }
}
