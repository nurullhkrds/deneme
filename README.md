    List<InstitutionChnnlPymMthdAccDTO> dtos = new ArrayList<>();

    for (Object[] row : rows) {
        dtos.add(toDTO(row));
    }

    return dtos;
