ockHttpServletRequest:
      HTTP Method = GET
      Request URI = /caching/evict
       Parameters = {cacheName=[testCache]}
          Headers = []
             Body = null
    Session Attrs = {}

Handler:
             Type = com.ykb.payments.bill.transaction.caching.web.CachingController
           Method = com.ykb.payments.bill.transaction.caching.web.CachingController#evictAllCacheValues(String)

Async:
    Async started = false
     Async result = null

Resolved Exception:
             Type = null

ModelAndView:
        View name = null
             View = null
            Model = null

FlashMap:
       Attributes = null

MockHttpServletResponse:
           Status = 200
    Error message = null
          Headers = []
     Content type = null
             Body = 
    Forwarded URL = null
   Redirected URL = null
          Cookies = []


Wanted but not invoked:
org.slf4j.Logger#0 bean.info(
    "request: {}",
    "testCache"
);
-> at com.ykb.payments.bill.transaction.caching.web.CachingControllerTest.evictAllCacheValues_shouldCallEvictAllCacheValues(CachingControllerTest.java:54)
Actually, there were zero interactions with this mock.

Wanted but not invoked:
org.slf4j.Logger#0 bean.info(
    "request: {}",
    "testCache"
);
-> at com.ykb.payments.bill.transaction.caching.web.CachingControllerTest.evictAllCacheValues_shouldCallEvictAllCacheValues(CachingControllerTest.java:54)
Actually, there were zero interactions with this mock.
