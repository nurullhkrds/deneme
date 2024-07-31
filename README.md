@Entity
@Getter
@Setter
public class PaymentMethod {
	
	@Id
	@Enumerated(EnumType.STRING)
	//@Column(nullable = false, length = 50)
	private EnumPaymentMethod code;
	
	@Column(nullable = false, length = 100)
	private String name;
		
	@Column(nullable = false, length = 500)
	private String explanation;
	
//	@OneToMany(mappedBy = "paymentMethod", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
//	private List<InstitutionPymMethod> institutionPymMethodList;
//		
//	@OneToMany(mappedBy = "paymentMethod", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
//	private List<InstitutionOrderPymMethod> institutionOrderPymMethodList;
//	
//	@OneToMany(mappedBy = "paymentMethod", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
//	private List<InstitutionChannelPymMethod> institutionChannelPymMethodList;
//	
//	@OneToMany(mappedBy = "paymentMethod", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
//	private List<OrderPaymentGroup> orderPaymentGroupList;

}
