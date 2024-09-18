public OwnerDepartmentDTO findByCode(String code) {
        return ownerDepartmentRepository.findByCode(code)
            .map(ownerDepartmentMapper::toOwnerDepartmentDTO)
            .orElseThrow(() -> {
                ExceptionData error = new ExceptionData();
                error.setErrorCode(400L);
                error.setErrorMessage(ResultConstant.OWNER_DEPARTMENT_NOT_FOUND.getMessage());
                error.setApplicationName(SERVICE_NAME);
                throw new DataNotFoundException(error);
            });
    }
