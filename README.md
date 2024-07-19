org.mockito.exceptions.base.MockitoException: 
Cannot instantiate @InjectMocks field named 'accountingService'! Cause: the type 'AccountingService' is an interface.
You haven't provided the instance at field declaration so I tried to construct the instance.
Examples of correct usage of @InjectMocks:
   @InjectMocks Service service = new Service();
   @InjectMocks Service service;
   //and... don't forget about some @Mocks for injection :)
