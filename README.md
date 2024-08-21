@Override
public DataResult<BaseReturnMapDTO> createReturnMap(CreateReturnMapRequest request) {
    // Veritabanında returnMapCode kontrolü
    Optional<BaseReturnMap> existingReturnMap = returnMapRepository.findByReturnMapCode(request.getReturnMapCode());

    if (existingReturnMap.isPresent()) {
        // Eğer kayıt zaten mevcutsa, uygun bir mesaj döndür
        return new ErrorDataResult<>("Bu kod ile zaten bir kayıt mevcut.");
    }

    // Yeni kayıt oluşturma işlemi
    BaseReturnMap newReturnMap = new BaseReturnMap();
    newReturnMap.setReturnMapCode(request.getReturnMapCode());
    newReturnMap.setIsActive(request.getIsActive());

    // Kayıt veritabanına eklenir
    baseReturnMapRepository.save(newReturnMap);

    // Başarılı bir sonuç döndürülür
    return new SuccessDataResult<>(new BaseReturnMapDTO(newReturnMap), "Kayıt başarıyla oluşturuldu.");
}
