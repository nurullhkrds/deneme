when(publishPaymentTypeDTO.getPaymentDTO()).thenReturn(paymentDTO);
        when(publishPaymentTypeDTO.getInstitutionDTO()).thenReturn(institutionDTO);
        when(paymentDTO.getPaymentMethod()).thenReturn(paymentMethod);
