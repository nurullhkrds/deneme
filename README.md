package com.ykb.payments.bill.transaction.institution.admin.service.impl;


import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminProductMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminProductService;
import com.ykb.payments.bill.transaction.institution.domain.Product;
import com.ykb.payments.bill.transaction.institution.dto.ProductDTO;
import com.ykb.payments.bill.transaction.institution.repository.ProductRepository;
import org.springframework.stereotype.Service;

@Service
public class AdminProductServiceImpl implements AdminProductService {


    private final ProductRepository productRepository;
    private final AdminProductMapper productMapper;


    public AdminProductServiceImpl(ProductRepository productRepository, AdminProductMapper productMapper) {
        this.productRepository = productRepository;
        this.productMapper = productMapper;
    }




    @Override
    public ProductDTO getProductByCode(String code) throws MicroException{

        Product entity= productRepository.findByCode(code);
        if (entity == null){
           throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.PRODUCT_NOT_FOUND);
        }
        ProductDTO dto= productMapper.toProductDTO(entity);
        return dto;

    }
}
