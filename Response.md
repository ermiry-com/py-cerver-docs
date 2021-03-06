# HTTP Response

**http_response_delete ()** correctly disposes a HTTP response and all of its data

``` python
http_response_delete (
    c_void_p    # reference to a HTTP response instance
) -> None
```

---

**http_response_get ()** gets a new HTTP response ready to be used

``` python
http_response_get () -> None
```

**Returns** a reference to a HTTP response instance

---

**http_response_return ()** correctly disposes a HTTP response

``` python
http_response_return (
    c_void_p    # reference to a HTTP response instance
) -> None
```

---

**http_response_set_status ()** sets the HTTP response's status code to be set in the header when compilling

``` python
http_response_set_status (
    c_void_p,   # reference to a HTTP response instance
    http_status # the response's status code
) -> None
```

---

**http_response_set_header ()** sets the response's header, it will replace the existing one. the data will be deleted when the response gets deleted

``` python
http_response_set_header (
    c_void_p,   # reference to a HTTP response instance
    c_void_p,   # a reference to the header value to be used
    c_size_t    # the response's header size
) -> None
```

---

**http_response_add_header ()** adds a new header to the response, the headers will be handled when calling http_response_compile () to generate a continuos header buffer

``` python
http_response_add_header (
    c_void_p,       # reference to a HTTP response instance
    http_header,    # the http_header header to be added
    c_char_p        # the real header value as a string reference
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_content_type_header ()** adds a "Content-Type" header to the response

``` python
http_response_add_content_type_header (
    c_void_p,       # reference to a HTTP response instance
    ContentType     # the response's ContentType value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_content_length_header ()** adds a "Content-Length" header to the response

``` python
http_response_add_content_length_header (
    c_void_p,   # reference to a HTTP response instance
    c_size_t    # the response's content size
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_json_headers ()** adds a "Content-Type" with value "application/json" and adds a "Content-Length" header to the response

``` python
http_response_add_json_headers (
    c_void_p,   # reference to a HTTP response instance
    c_size_t    # the response's content size
) -> c_uint8
```

---

**http_response_add_cors_header ()** adds an "Access-Control-Allow-Origin" header to the response

``` python
http_response_add_cors_header (
    c_void_p,   # reference to a HTTP response instance
    c_char_p    # the domain to be used
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_cors_header_from_origin ()** works like http_response_add_cors_header () but takes a HttpOrigin instead of a c string

``` python
http_response_add_cors_header_from_origin (
    c_void_p,   # reference to a HTTP response instance
    c_void_p    # reference to a HTTP origin instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_whitelist_cors_header ()** works like http_response_add_cors_header () but first checks if the domain matches any entry in the whitelist

``` python
http_response_add_whitelist_cors_header (
    c_void_p,   # reference to a HTTP response instance
    c_char_p    # the domain to be used
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_whitelist_cors_header_from_origin ()** works like http_response_add_whitelist_cors_header () but takes a HttpOrigin instead of a c string

``` python
http_response_add_whitelist_cors_header_from_origin (
    c_void_p,   # reference to a HTTP response instance
    c_void_p    # reference to a HTTP origin instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_whitelist_cors_header_from_request ()** checks if the HTTP request's "Origin" header value matches any domain in the whitelist then adds an "Access-Control-Allow-Origin" header to the response

``` python
http_response_add_whitelist_cors_header_from_request (
    c_void_p,   # reference to a HTTP receive instance
    c_void_p    # reference to a HTTP response instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_cors_allow_credentials_header ()** sets CORS related header "Access-Control-Allow-Credentials". This header is needed when a CORS request has an "Authorization" header

``` python
http_response_add_cors_allow_credentials_header (
    c_void_p    # reference to a HTTP response instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_cors_allow_methods_header ()** sets CORS related header "Access-Control-Allow-Methods" to be a list of available methods. This header is needed in preflight OPTIONS request's responses

``` python
http_response_add_cors_allow_methods_header (
    c_void_p,   # reference to a HTTP response instance
    c_char_p    # list of available methods like "GET, HEAD, OPTIONS"
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_set_data ()** sets the response's data (body), it will replace the existing one. The data will be deleted when the response gets deleted

``` python
http_response_set_data (
    c_void_p,   # reference to a HTTP response instance
    c_void_p,   # a reference to the response's body value
    c_size_t    # the response's body size
) -> None
```

---

**http_response_set_data_ref ()** sets a reference to a data buffer to send. Data will not be copied into the response and will not be freed after use. This method is similar to packet_set_data_ref ()

``` python
http_response_set_data_ref (
    c_void_p,   # reference to a HTTP response instance
    c_void_p,   # a reference to the response's body value
    c_size_t    # the response's body size
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_create ()** creates a new HTTP response with the specified status code. Ability to set the response's data (body); it will be copied to the response and the original data can be safely deleted

``` python
http_response_create (
    http_status,    # the response's status code
    c_void_p,       # an optional string value as the response's body
    c_size_t        # the response's body string size
) -> c_void_p
```

**Returns**

---

**http_response_compile ()** merges the response header and the data into the final response

``` python
http_response_compile (
    c_void_p    # reference to a HTTP response instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_print ()** prints the compiled HTTP response

``` python
http_response_print (
    c_void_p    # reference to a HTTP response instance
) -> None
```

## Send

**http_response_send ()** sends a response through the connection's socket

``` python
http_response_send (
    c_void_p,   # reference to a HTTP response instance
    c_void_p    # reference to a HttpReceive instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_send_split ()** expects a response with an already created header and data as this method will send both parts without the need of a continuos response buffer. Use this for maximun efficiency

``` python
http_response_send_split (
    c_void_p,   # reference to a HTTP response instance
    c_void_p    # reference to a HttpReceive instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_create_and_send ()** creates & sends a response through the connection's socket

``` python
http_response_create_and_send (
    http_status,    # reference to a HTTP response instance
    c_void_p,       # an optional string value as the response's body
    c_size_t,       # the response's body string size
    c_void_p        # reference to a HttpReceive instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_send_response ()** creates and sends a HTTP response based on the supplied body

``` python
http_send_response (
    http_receive: c_void_p,
    status_code: http_status,
	body=None,
    body_len=None,
	content_type=HTTP_CONTENT_TYPE_HTML
) -> None
```

## Files

**http_response_send_file ()** opens the selected file and sends it back to the client and takes care of generating the header based on the file values

``` python
http_response_send_file (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p        # the filename to be used
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_send_file (
    http_receive, HTTP_STATUS_OK,
    b"./examples/http/public/index.html"
)
```

## Render

**http_response_render_text ()** sends the selected text back to the user. This methods takes care of generating a repsonse with text/html content type

``` python
http_response_render_text (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p,       # a string reference to the TEXT body
    c_size_t    	# the TEXT string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
text = b"<!DOCTYPE html><html><head><meta charset=\"utf-8\" /><title>Cerver</title></head><body><h2>text_handler () works!</h2></body></html>"
text_len = len (text)

http_response_render_text (
    http_receive, HTTP_STATUS_OK,
    text, text_len
)
```

---

**http_response_render_json ()** sends the selected json back to the user. This methods takes care of generating a repsonse with application/json content type

``` python
http_response_render_json (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p,       # a string reference to the JSON body
    c_size_t        # the JSON string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
json =  b"{\"msg\": \"okay\"}"
json_len = len (json)

http_response_render_json (
    http_receive, HTTP_STATUS_OK,
    json, json_len
)
```

## Videos

**http_response_handle_video ()** handles the transmission of a video to the client

``` python
http_response_handle_video (
    c_void_p,       # reference to a HttpReceive instance
    c_char_p        # the filename to be used
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_handle_video (
    http_receive, b"./data/videos/redis.webm"
)
```

## JSON

**http_response_create_json ()** creates a HTTP response with the defined status code with a custom json message body

``` python
http_response_create_json (
    http_status,    # the response's status code
    c_char_p,       # a JSON string to be used as the body
    c_size_t        # the size of the JSON string
) -> c_void_p
```

**Returns** a new HTTP response instance ready to be sent

**Example**
``` python
json_res = b"{\"msg\": \"okay\"}"
json_len = len (json_res)

response = http_response_create_json (
    HTTP_STATUS_OK, json_res, json_len
)

http_response_send (response, http_receive)
http_response_delete (response)
```

---

**http_response_create_json_key_value ()** creates a HTTP response with the defined status code and a data (body) with a json message of type { key: value } that is ready to be sent

``` python
http_response_create_json_key_value (
    http_status,    # the response's status code
    c_char_p,       # the JSON message "key" string value
    c_char_p        # the JSON message "value" string value
) -> c_void_p
```

**Returns** a new HTTP response instance

**Example**
``` python
response = http_response_create_json_key_value (
    HTTP_STATUS_OK, b"msg", b"okay"
)

http_response_print (response)
http_response_send (response, http_receive)
http_response_delete (response)
```

---

**http_response_json_int_value ()** creates a HTTP response with the defined status code with a json body of type { "key": int_value }

``` python
http_response_json_int_value (
    http_status,    # the response's status code
    c_char_p,       # the JSON "key" string value
    c_int           # the int value to be used
) -> c_void_p
```

**Returns** a new HTTP response instance ready to be sent

**Example**
``` python
value = 18
response = http_response_json_int_value (
    HTTP_STATUS_OK, b"integer", value
)

http_response_send (response, http_receive)
http_response_delete (response)
```

---

**http_response_json_int_value_send ()** sends a HTTP response with custom status code with a json body of type { "key": int_value }

``` python
http_response_json_int_value_send (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p,       # the JSON "key" string value
    c_int           # the int value to be used
) -> c_void_p
```

**Returns** 0 on success, 1 on error

**Example**
``` python
value = 18
http_response_json_int_value_send (
    http_receive, HTTP_STATUS_OK,
    b"integer", value
)
```

---

**http_response_json_large_int_value ()** creates a HTTP response with the defined status code with a json body of type { "key": large_int_value }

``` python
http_response_json_large_int_value (
    http_status,    # the response's status code
    c_char_p,       # the JSON "key" string value
    c_long          # the large int value to be used
) -> c_void_p
```

**Returns** a new HTTP response instance ready to be sent

**Example**
``` python
value = 1800000
response = http_response_json_large_int_value (
    HTTP_STATUS_OK, b"large", value
)

http_response_send (response, http_receive)
http_response_delete (response)
```

---

**http_response_json_large_int_value_send ()** sends a HTTP response with custom status code with a json body of type { "key": large_int_value }

``` python
http_response_json_large_int_value_send (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p,       # the JSON "key" string value
    c_long          # the large int value to be used
) -> c_void_p
```

**Returns** 0 on success, 1 on error

**Example**
``` python
value = 1800000
http_response_json_large_int_value_send (
    http_receive, HTTP_STATUS_OK,
    b"large", value
)
```

---

**http_response_json_real_value ()** creates a HTTP response with the defined status code with a json body of type { "key": double_value }

``` python
http_response_json_real_value (
    http_status,    # the response's status code
    c_char_p,       # the JSON "key" string value
    c_double        # the real value to be used
) -> c_void_p
```

**Returns** a new HTTP response instance ready to be sent

**Example**
``` python
value = 18.123
response = http_response_json_real_value (
    HTTP_STATUS_OK, b"real", value
)

http_response_send (response, http_receive)
http_response_delete (response)
```

---

**http_response_json_real_value_send ()** sends a HTTP response with custom status code with a json body of type { "key": double_value }

``` python
http_response_json_real_value_send (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p,       # the JSON "key" string value
    c_double        # the real value to be used
) -> c_void_p
```

**Returns** 0 on success, 1 on error

**Example**
``` python
value = 18.123
http_response_json_real_value_send (
    http_receive, HTTP_STATUS_OK,
    b"real", value
)
```

---

**http_response_json_bool_value ()** creates a HTTP response with the defined status code with a json body of type { "key": bool_value }

``` python
http_response_json_bool_value (
    http_status,    # the response's status code
    c_char_p,       # the JSON "key" string value
    c_bool          # the bool value to be used
) -> c_void_p
```

**Returns** a new HTTP response instance ready to be sent

**Example**
``` python
value = True
response = http_response_json_bool_value (
    HTTP_STATUS_OK, b"bool", value
)

http_response_send (response, http_receive)
http_response_delete (response)
```

---

**http_response_json_bool_value_send ()** sends a HTTP response with custom status code with a json body of type { "key": bool_value }

``` python
http_response_json_bool_value_send (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p,       # the JSON "key" string value
    c_bool          # the bool value to be used
) -> c_void_p
```

**Returns** 0 on success, 1 on error

**Example**
``` python
value = True
http_response_json_bool_value_send (
    http_receive, HTTP_STATUS_OK,
    b"bool", value
)
```

---

**http_response_json_msg ()** creates a HTTP response with the defined status code and a data (body) with a json message of type { msg: "your message" } ready to be sent

``` python
http_response_json_msg (
    http_status,    # the response's status code
    c_char_p        # the JSON "your message" string value
) -> c_void_p
```

**Returns** a new HTTP response instance

**Example**
``` python
response = http_response_json_msg (
    HTTP_STATUS_OK, b"Test route works!"
)

http_response_print (response)
http_response_send (response, http_receive)
http_response_delete (response)
```

---

**http_response_json_msg_send ()** creates and sends a HTTP json message response with the defined status code & message

``` python
http_response_json_msg_send (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p        # the JSON "your message" string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_json_msg_send (
    http_receive, HTTP_STATUS_OK, b"Hola handler!"
)
```

---

**http_response_json_error ()** creates a HTTP response with the defined status code and a data (body) with a json message of type { error: "your error message" } ready to be sent

``` python
http_response_json_error (
    http_status,    # the response's status code
    c_char_p        # the JSON "your error message" string value
) -> c_void_p
```

**Returns** a new HTTP response instance

**Example**
``` python
res = http_response_json_error (
    HTTP_STATUS_BAD_REQUEST, b"bad request"
)

http_response_print (res)
http_response_send (res, http_receive)
http_response_delete (res)
```

---

**http_response_json_error_send ()** creates and sends a HTTP json error response with the defined status code & message

``` python
http_response_json_error_send (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p        # the JSON "your error message" string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_json_error_send (
    http_receive, HTTP_STATUS_BAD_REQUEST, b"Missing values!"
)
```

---

**http_response_json_key_value ()** creates a HTTP response with the defined status code and a data (body) with a json meesage of type { key: value } ready to be sent

``` python
http_response_json_key_value (
    http_status,    # the response's status code
    c_char_p,       # the JSON message "key" string value
    c_char_p        # the JSON message "value" string value
) -> c_void_p
```

**Returns** a new HTTP response instance

---

**http_response_json_key_value_send ()** creates and sends a HTTP custom json response with the defined status code & key-value

``` python
http_response_json_key_value_send (
    c_void_p,       # reference to a HttpReceive instance
    http_status,    # the response's status code
    c_char_p,       # the JSON message "key" string value
    c_char_p        # the JSON message "value" string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_json_key_value_send (
    http_receive, HTTP_STATUS_OK,
    b"key", b"value"
)
```
