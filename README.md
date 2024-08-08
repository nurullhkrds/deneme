
org.mockito.exceptions.misusing.CannotStubVoidMethodWithReturnValue: 
'close' is a *void method* and it *cannot* be stubbed with a *return value*!
Voids are usually stubbed with Throwables:
    doThrow(exception).when(mock).someVoidMethod();
If you need to set the void method to do nothing you can use:
    doNothing().when(mock).someVoidMethod();
For more information, check out the javadocs for Mockito.doNothing().
***
If you're unsure why you're getting above error read on.
Due to the nature of the syntax above problem might occur because:
1. The method you are trying to stub is *overloaded*. Make sure you are calling the right overloaded version.
2. Somewhere in your test you are stubbing *final methods*. Sorry, Mockito does not verify/stub final methods.
3. A spy is stubbed using when(spy.foo()).then() syntax. It is safer to stub spies - 
   - with doReturn|Throw() family of methods. More in javadocs for Mockito.spy() method.
4. Mocking methods declared on non-public parent classes is not supported.
