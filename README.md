List<Object[]> rows = // SQL sorgunuzdan dönen sonuçlar
List<InstitutionChnlPymMthdAccWebDTO> dtos = rows.stream()
    .map(mapper::fromObjectArray) // `fromObjectArray` metodunu kullan
    .collect(Collectors.toList());
