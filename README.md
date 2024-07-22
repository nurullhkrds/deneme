org.mockito.exceptions.misusing.InvalidUseOfMatchersException: 
Misplaced or misused argument matcher detected here:

-> at com.ykb.payments.bill.transaction.accounting.provision.service.CardProvisionServiceImplTest.whenCreateAccountingResultDTOisFailed(CardProvisionServiceImplTest.java:182)
-> at com.ykb.payments.bill.transaction.accounting.provision.service.CardProvisionServiceImplTest.whenCreateAccountingResultDTOisFailed(CardProvisionServiceImplTest.java:182)

You cannot use argument matchers outside of verification or stubbing.
Examples of correct usage of argument matchers:
    when(mock.get(anyInt())).thenReturn(null);
    doThrow(new RuntimeException()).when(mock).someVoidMethod(any());
    verify(mock).someMethod(contains("foo"))

This message may appear after an NullPointerException if the last matcher is returning an object 
like any() but the stubbed method signature expect a primitive argument, in this case,
use primitive alternatives.
    when(mock.get(any())); // bad use, will raise NPE
    when(mock.get(anyInt())); // correct usage use

Also, this error might show up because you use argument matchers with methods that cannot be mocked.
Following methods *cannot* be stubbed/verified: final/private/equals()/hashCode().
Mocking methods declared on non-public parent classes is not supported.
