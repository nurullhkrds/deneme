@Override
	public DataResult<InstitutionDTO> updateInstitution(UpdateInstitutionRequest request) throws MicroException {
		Institution existingInstitution = institutionRepository.findById(request.getId())
				.orElseThrow(() -> {
					ExceptionData error = new ExceptionData();
					error.setErrorCode(404L);
					error.setErrorMessage(ResultConstant.INSTITUTION_NOT_FOUND.getMessage());
					error.setApplicationName(SERVICE_NAME);
					return new DataNotFoundException(error);
				});

		Optional<Institution> duplicateInstitution = institutionRepository.findByProductCodeAndInstitutionCode(request.getProductCode(), request.getInstitutionCode());
		if (duplicateInstitution.isPresent() && !duplicateInstitution.get().getId().equals(request.getId())) {
			ExceptionData error = new ExceptionData();
			error.setErrorCode(409L);
			error.setErrorMessage(ResultConstant.DUPLICATE_INSTITUTION_PRODUCT.getMessage(request.getInstitutionCode(), request.getProductCode()));
			error.setApplicationName(SERVICE_NAME);
			throw new DataConflictException(error);
		}

		ProductDTO productDTO = productRepository.findByCode(request.getProductCode())
				.map(productMapper::toProductDTO)
				.orElseThrow(() -> {
					ExceptionData error = new ExceptionData();
					error.setErrorCode(400L);
					error.setErrorMessage(ResultConstant.PRODUCT_NOT_FOUND.getMessage());
					error.setApplicationName(SERVICE_NAME);
					return new DataNotFoundException(error);
				});

		OwnerDepartmentDTO ownerDepartmentDTO = ownerDepartmentRepository.findByCode(request.getOwnerDepartmentCode())
				.map(ownerDepartmentMapper::toOwnerDepartmentDTO)
				.orElseThrow(() -> {
					ExceptionData error = new ExceptionData();
					error.setErrorCode(400L);
					error.setErrorMessage(ResultConstant.OWNER_DEPARTMENT_NOT_FOUND.getMessage());
					error.setApplicationName(SERVICE_NAME);
					return new DataNotFoundException(error);
				});

		InstitutionDTO institutionDTO = institutionMapper.toInstitutionDTO(request);
		institutionDTO.setUpdateDate(LocalDateTime.now());

		Institution updatedInstitution = institutionMapper.toInstitution(institutionDTO);

		institutionRepository.save(updatedInstitution);

		InstitutionDTO updatedDTO = institutionMapper.toInstitutionDTO(updatedInstitution);

		return new SuccessDataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(), updatedDTO, 200);
	}



024-09-17 18:04:06,796 ERROR [http-nio-8081-exec-5][SqlExceptionHelper] ORA-00001: benzersiz kural (BILL.UIDX_INSTITUTION_01) ihlal edildi

2024-09-17 18:04:06,797 INFO [http-nio-8081-exec-5][AbstractBatchImpl] HHH000010: On release of batch it still contained JDBC statements
2024-09-17 18:04:06,810 ERROR [http-nio-8081-exec-5][PymExceptionHandler] An Exception occured org.springframework.dao.DataIntegrityViolationException: could not execute statement; SQL [n/a]; constraint [BILL.UIDX_INSTITUTION_01]; nested exception is org.hibernate.exception.ConstraintViolationException: could not execute statement
	at org.springframework.orm.jpa.vendor.HibernateJpaDialect.convertHibernateAccessException(HibernateJpaDialect.java:276)
	at org.springframework.orm.jpa.vendor.HibernateJpaDialect.translateExceptionIfPossible(HibernateJpaDialect.java:233)
	at org.springframework.orm.jpa.JpaTransactionManager.doCommit(JpaTransactionManager.java:566)
	at org.springframework.transaction.support.AbstractPlatformTransactionManager.processCommit(AbstractPlatformTransactionManager.java:743)
	at org.springframework.transaction.support.AbstractPlatformTransactionManager.commit(AbstractPlatformTransactionManager.java:711)
	at org.springframework.transaction.interceptor.TransactionAspectSupport.commitTransactionAfterReturning(TransactionAspectSupport.java:654)
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:407)
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:119)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
	at org.springframework.dao.support.PersistenceExceptionTranslationInterceptor.invoke(PersistenceExceptionTranslationInterceptor.java:137)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:186)
	at org.springframework.data.jpa.repository.support.CrudMethodMetadataPostProcessor$CrudMethodMetadataPopulatingMethodIntercept
