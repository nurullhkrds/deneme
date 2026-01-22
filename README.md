Caused by: java.lang.IllegalArgumentException: Validation failed for query for method public abstract void com.ykb.payments.bill.transaction.order.repository.PaymentOrderDetailRepository.updatePaymentOrderDetailActivityByOrderId(java.lang.Long,boolean)


@Modifying
    @Transactional
    @Query("UPDATE PaymentOrderDetail SET IS_ACTIVE = :isActive WHERE orderId = :orderId")
    void updatePaymentOrderDetailActivityByOrderId (@Param("orderId") Long orderId, @Param("active") boolean isActive);


	@Entity
@Getter
@Setter
@Audited
@DynamicUpdate
public class PaymentOrderDetail extends UpdatableBaseEntity {


    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "ORDER_DETAIL_ID_GENERATOR")
    @SequenceGenerator(name = "ORDER_DETAIL_ID_GENERATOR", sequenceName = "SEQ_ORDER_DETAIL", allocationSize = 1)
    @Column(nullable = false, precision = 16, scale = 0)
    private Long id;


    @Column(name = "order_id", length = 16, nullable = false)
    private Long orderId;

    @Column(name = "order_sequence_no", length = 16, nullable = false)
    private Long orderSequenceNo;

    @Column(name = "sequence_no", length = 16, nullable = false)
    private int sequenceNo;

    @Column(name = "account_no", length = 9, nullable = false)
    private String accountNo;

    @Column(name = "account_branch_code", length = 4, nullable = false)
    private String accountBranchCode;


    @Column(name = "card_no", length = 19, nullable = false)
    private String cardNo;

    @Column(name = "currency",nullable = false)
    @Convert(converter = EnumCurrencyCodeConverter.class)
    private EnumCurrencyCode currency;

    @Column(nullable= false)
    private Boolean isActive;


}
