@Query(value = """
SELECT por.*
FROM bill.payment_order por
JOIN bill.subscriber s
  ON s.id = por.subscriber_id
WHERE por.customer_no = :customerNo
  AND por.status      = :status
  AND s.status        = :status
  AND s.subscriber_no = :subscriberNo
  AND EXISTS (
      SELECT 1
      FROM bill.institution i
      WHERE i.id = s.institution_id
        AND i.institution_code = :institutionCode
        AND i.product_code     = :productCode
  )
""", nativeQuery = true)
List<PaymentOrderEntity> findPaymentOrdersNative(
        @Param("customerNo") Long customerNo,
        @Param("status") String status,
        @Param("subscriberNo") String subscriberNo,
        @Param("institutionCode") String institutionCode,
        @Param("productCode") String productCode
);
