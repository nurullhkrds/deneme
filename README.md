 // Sıralama kriteri belirleme
        Sort sort = Sort.by(Sort.Direction.ASC, "returnMapCode");

        // Sorgu yap ve sıralı sonuçları al
        return returnMapRepository.findAll(spec, sort);
