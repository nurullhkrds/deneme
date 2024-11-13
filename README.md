package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminProductMapper;
import com.ykb.payments.bill.transaction.institution.domain.Product;
import com.ykb.payments.bill.transaction.institution.dto.ProductDTO;
import com.ykb.payments.bill.transaction.institution.repository.ProductRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

public class AdminProductServiceTest {

    @InjectMocks
    private AdminProductServiceImpl adminProductService;

    @Mock
    private ProductRepository productRepository;

    @Mock
    private AdminProductMapper productMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getProductByCode_WhenFound_ShouldReturnDTO() throws MicroException {
        String code = "PROD123";
        Product productEntity = new Product();
        ProductDTO productDTO = new ProductDTO();

        when(productRepository.findByCode(code)).thenReturn(productEntity);
        when(productMapper.toProductDTO(productEntity)).thenReturn(productDTO);

        ProductDTO result = adminProductService.getProductByCode(code);

        assertNotNull(result);
        verify(productRepository, times(1)).findByCode(code);
        verify(productMapper, times(1)).toProductDTO(productEntity);
    }

    @Test
    void getProductByCode_WhenNotFound_ShouldThrowException() {
        String code = "PROD123";

        when(productRepository.findByCode(code)).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminProductService.getProductByCode(code));
        verify(productRepository, times(1)).findByCode(code);
    }
}
