MockHttpServletRequest:
      HTTP Method = OPTIONS
      Request URI = /**
       Parameters = {}
          Headers = [Origin:"http://localhost:3000", Access-Control-Request-Method:"GET"]
             Body = null
    Session Attrs = {}

Handler:
             Type = null

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
          Headers = [Access-Control-Allow-Methods:"POST, GET, OPTIONS, DELETE, PUT, PATCH", "GET,POST,PUT,DELETE,OPTIONS", Access-Control-Max-Age:"3600", "1800", Access-Control-Expose-Headers:"X-Session-Id", Vary:"Origin", "Access-Control-Request-Method", "Access-Control-Request-Headers", Access-Control-Allow-Origin:"*", X-Content-Type-Options:"nosniff", X-XSS-Protection:"1; mode=block", Cache-Control:"no-cache, no-store, max-age=0, must-revalidate", Pragma:"no-cache", Expires:"0", X-Frame-Options:"DENY"]
     Content type = null
             Body = 
    Forwarded URL = null
   Redirected URL = null
          Cookies = []

java.lang.AssertionError: Response header 'Access-Control-Allow-Origin' expected:<http://localhost:3000> but was:<*>
Expected :http://localhost:3000
Actual   :*
