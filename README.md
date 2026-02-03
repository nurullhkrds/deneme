SELECT /*+ leading(s) use_nl(por) */
       por.*
FROM bill.payment_order por
JOIN (
    SELECT s.id
    FROM bill.subscriber s
    WHERE s.status = 'ORDERED'
      AND s.subscriber_no = '1257@/'
      AND EXISTS (
          SELECT 1
          FROM bill.institution i
          WHERE i.id = s.institution_id
            AND i.institution_code = 'BALIKESİR'
            AND i.product_code     = 'SU'
      )
) s1
  ON por.subscriber_id = s1.id
WHERE por.customer_no = 6395288
  AND por.status      = 'ORDERED';
