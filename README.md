@Override
public DataResult<BaseReturnMapDTO> createBaseReturnMap(CreateBaseReturnMapRequest request) {

    Optional<BaseReturnMap> existingReturnMap = baseReturnMapRepository.findByReturnMapCode(request.getReturnMapCode());

    if (existingReturnMap.isPresent()) {
        return new ErrorDataResult<>("Bu kod ile zaten bir kayıt mevcut.", null, 400);
    }

    BaseReturnMap newReturnMap = new BaseReturnMap();
    newReturnMap.setReturnMapCode(request.getReturnMapCode());
    newReturnMap.setIsActive(request.getIsActive());

    // Kayıt veritabanına eklenir
    BaseReturnMap savedData = baseReturnMapRepository.save(newReturnMap);

    // Saved data'dan DTO'ya dönüşüm yapılır
    BaseReturnMapDTO dto = baseReturnMapMapper.toBaseReturnMapDTO(savedData);

    // Eğer DTO null ise hata sonucu döndürülür
    if (dto == null) {
        return new ErrorDataResult<>("DTO'ya dönüştürme işlemi başarısız oldu.", null, 500);
    }

    // Başarılı bir şekilde DTO döndürülür
    return new SuccessDataResult<>(dto, "Kayıt başarıyla oluşturuldu.");
}
