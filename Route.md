# HTTP Route

**HttpRouteModifier**

HTTP_ROUTE_MODIFIER_NONE \
HTTP_ROUTE_MODIFIER_MULTI_PART \
HTTP_ROUTE_MODIFIER_WEB_SOCKET

**HttpRouteAuthType**

HTTP_ROUTE_AUTH_TYPE_NONE \
HTTP_ROUTE_AUTH_TYPE_BEARER

**HttpHandler** = CFUNCTYPE (None, c_void_p, c_void_p)

**HttpDecodeData** = CFUNCTYPE (c_void_p, c_void_p) \
**HttpDeleteDecoded** = CFUNCTYPE (None, c_void_p)

**AuthenticationHandler** = CFUNCTYPE (c_uint, c_void_p, c_void_p)

---

**http_route_create ()** creates a new route that can be registered to be used by an http cerver

``` python
http_route_create (
    RequestMethod,  # the method this route will handler (GET, POST, ...)
    c_char_p,       # the actual route path
    HttpHandler     # the callback handler method
) -> c_void_p
```

**Returns** a new instance of HttpRoute

---

**http_route_set_handler ()** sets the route's handler for the selected HTTP method

``` python
http_route_set_handler (
    c_void_p,       # an already created HTTP route instance
    RequestMethod,  # the request method to handle (GET, POST, ...)
    HttpHandler	    # the callback handler method
) -> None
```

---

**http_route_child_add ()** registers a route as a child of a parent route

``` python
http_route_child_add (
    c_void_p,   # the parent HTTP route instance
    c_void_p    # the child HTTP route instance
) -> None
```

---

**http_route_set_modifier ()** sets a modifier for the selected route

``` python
http_route_set_modifier (
    c_void_p,           # a HTTP route instance
    HttpRouteModifier   # the desired HTTP route modifier value
) -> None
```

---

**http_route_set_auth ()** enables authentication for the selected route

``` python
http_route_set_auth (
    c_void_p,           # a HTTP route instance
    HttpRouteAuthType   # the desired HTTP route authentication method to be used
) -> None
```

---

**http_route_set_decode_data ()** sets the method to be used to decode incoming data from jwt & a method to delete it after use. If no delete method is set, data won't be freed

``` python
http_route_set_decode_data (
    c_void_p,           # a HTTP route instance
    HttpDecodeData,     # the callback method used to decode data
    HttpDeleteDecoded   # the callback method used to free decoded data
) -> None
```

---

**http_route_set_decode_data_into_json ()** sets a method to decode data from a jwt into a json string

``` python
http_route_set_decode_data_into_json (
    c_void_p    # a HTTP route instance
) -> None
```

---

**http_route_set_authentication_handler ()** sets a method to be used to handle auth in a private route that has been configured with HTTP_ROUTE_AUTH_TYPE_CUSTOM.
Method must return 0 on success and 1 on error

``` python
http_route_set_authentication_handler (
    c_void_p                # a HTTP route instance
    AuthenticationHandler   # the authentication callback to be used
) -> None
```
