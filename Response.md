# HTTP Response

**http_response_delete ()** correctly disposes a HTTP response and all of its data

``` python
http_response_delete (
	c_void_p			# reference to a HTTP response instance
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
	c_void_p			# reference to a HTTP response instance
) -> None
```

---

**http_response_set_status ()** sets the HTTP response's status code to be set in the header when compilling

``` python
http_response_set_status (
	c_void_p,			# reference to a HTTP response instance
	http_status			# the response's status code
) -> None
```

---

**http_response_set_header ()** sets the response's header, it will replace the existing one. the data will be deleted when the response gets deleted

``` python
http_response_set_header (
	c_void_p,			# reference to a HTTP response instance
	c_void_p,			# a reference to the header value to be used
	c_size_t			# the response's header size
) -> None
```

---

**http_response_add_header ()** adds a new header to the response, the headers will be handled when calling http_response_compile () to generate a continuos header buffer

``` python
http_response_add_header (
	c_void_p,			# reference to a HTTP response instance
	HttpHeader,			# the HttpHeader header to be added
	c_char_p			# the real header value as a string reference
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_content_type_header ()** adds a HTTP_HEADER_CONTENT_TYPE header to the response

``` python
http_response_add_content_type_header (
	c_void_p,			# reference to a HTTP response instance
	ContentType			# the response's ContentType value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_add_content_length_header ()** adds a HTTP_HEADER_CONTENT_LENGTH header to the response

``` python
http_response_add_content_length_header (
	c_void_p,			# reference to a HTTP response instance
	c_size_t			# the response's content size
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_set_data ()** sets the response's data (body), it will replace the existing one. The data will be deleted when the response gets deleted

``` python
http_response_set_data (
	c_void_p,			# reference to a HTTP response instance
	c_void_p,			# a reference to the response's body value
	c_size_t			# the response's body size
) -> None
```

---

**http_response_set_data_ref ()** sets a reference to a data buffer to send. Data will not be copied into the response and will not be freed after use. This method is similar to packet_set_data_ref ()

``` python
http_response_set_data_ref (
	c_void_p,			# reference to a HTTP response instance
	c_void_p,			# a reference to the response's body value
	c_size_t			# the response's body size
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_create ()** creates a new HTTP response with the specified status code. Ability to set the response's data (body); it will be copied to the response and the original data can be safely deleted

``` python
http_response_create (
	http_status,		# the response's status code
	c_void_p,			# an optional string value as the response's body
	c_size_t			# the response's body string size
) -> c_void_p
```

**Returns**

---

**http_response_compile ()** merges the response header and the data into the final response

``` python
http_response_compile (
	c_void_p			# reference to a HTTP response instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_print ()** prints the compiled HTTP response

``` python
http_response_print (
	c_void_p			# reference to a HTTP response instance
) -> None
```

## Send

**http_response_send ()** sends a response through the connection's socket

``` python
http_response_send (
	c_void_p,			# reference to a HTTP response instance
	c_void_p			# reference to a HttpReceive instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_send_split ()** expects a response with an already created header and data as this method will send both parts without the need of a continuos response buffer. Use this for maximun efficiency

``` python
http_response_send_split (
	c_void_p,			# reference to a HTTP response instance
	c_void_p			# reference to a HttpReceive instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_response_create_and_send ()** creates & sends a response through the connection's socket

``` python
http_response_create_and_send (
	http_status			# reference to a HTTP response instance
	c_void_p,			# an optional string value as the response's body
	c_size_t,			# the response's body string size
	c_void_p,			# reference to a HttpReceive instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_send_response ()** Function to create and send a HTTP response

``` python
http_send_response (
	http_receive,		# the receive structure associated with the current request
	status_code,		# HTTP status code value
	body,				# value(s) to send in the response's body
	body_len,			# size of the body to send. Defaults to None (Will be calculated)
	content_type		# the response's body content type. If body is dict then content_type will be application/json. Defaults to text/html; charset=UTF-8
) -> None
```

## Render

**http_response_render_text ()** sends the selected text back to the user. This methods takes care of generating a repsonse with text/html content type

``` python
http_response_render_text (
	c_void_p,			# reference to a HttpReceive instance
	http_status,		# the response's status code
	c_char_p,			# a string reference to the TEXT body
	c_size_t			# the TEXT string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
text = "<!DOCTYPE html><html><head><meta charset=\"utf-8\" /><title>Cerver</title></head><body><h2>text_handler () works!</h2></body></html>".encode ('utf-8')
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
	c_void_p,			# reference to a HttpReceive instance
	http_status,		# the response's status code
	c_char_p,			# a string reference to the JSON body
	c_size_t			# the JSON string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
json =  "{\"msg\": \"okay\"}".encode ('utf-8')
json_len = len (json)

http_response_render_json (
	http_receive, HTTP_STATUS_OK,
	json, json_len
)
```

---

**http_response_render_file ()** opens the selected file and sends it back to the user. This method takes care of generating the header based on the file values

``` python
http_response_render_file (
	c_void_p,			# reference to a HttpReceive instance
	http_status,		# the response's status code
	c_char_p			# the filename to be used
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_render_file (
	http_receive, HTTP_STATUS_OK,
	"./examples/public/index.html".encode ('utf-8')
)
```

## JSON

**http_response_create_json ()** creates a HTTP response with the defined status code and a json data (body) that is ready to be sent

``` python
http_response_create_json (
	http_status,		# the response's status code
	c_char_p,			# a JSON string to be used as the body
	c_size_t			# the size of the JSON string
) -> c_void_p
```

**Returns** a new HTTP response instance

**Example**
``` python
json =  "{\"msg\": \"okay\"}".encode ('utf-8')
json_len = len (json)

res = http_response_create_json (
	HTTP_STATUS_OK, json, json_len
)

http_response_print (res)
http_response_send (res, http_receive)
http_response_delete (res)
```

---

**http_response_create_json_key_value ()** creates a HTTP response with the defined status code and a data (body) with a json message of type { key: value } that is ready to be sent

``` python
http_response_create_json_key_value (
	http_status,		# the response's status code
	c_char_p,			# the JSON message "key" string value
	c_char_p			# the JSON message "value" string value
) -> c_void_p
```

**Returns** a new HTTP response instance

**Example**
``` python
res = http_response_create_json_key_value (
	HTTP_STATUS_OK, "msg".encode ('utf-8'), "okay".encode ('utf-8')
)

http_response_print (res)
http_response_send (res, http_receive)
http_response_delete (res)
```

---

**http_response_json_msg ()** creates a HTTP response with the defined status code and a data (body) with a json message of type { msg: "your message" } ready to be sent

``` python
http_response_json_msg (
	http_status			# the response's status code
	c_char_p			# the JSON "your message" string value
) -> c_void_p
```

**Returns** a new HTTP response instance

**Example**
``` python
response = http_response_json_msg (
	HTTP_STATUS_OK, "Test route works!".encode ('utf-8')
)

http_response_print (response)
http_response_send (response, http_receive)
http_response_delete (response)
```

---

**http_response_json_msg_send ()** creates and sends a HTTP json message response with the defined status code & message

``` python
http_response_json_msg_send (
	c_void_p,			# reference to a HttpReceive instance
	http_status,		# the response's status code
	c_char_p			# the JSON "your message" string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_json_msg_send (
	http_receive, HTTP_STATUS_OK, "Hola handler!".encode ('utf-8')
)
```

---

**http_response_json_error ()** creates a HTTP response with the defined status code and a data (body) with a json message of type { error: "your error message" } ready to be sent

``` python
http_response_json_error (
	http_status,		# the response's status code
	c_char_p			# the JSON "your error message" string value
) -> c_void_p
```

**Returns** a new HTTP response instance

**Example**
``` python
res = http_response_json_error (
	HTTP_STATUS_BAD_REQUEST, "bad request".encode ('utf-8')
)

http_response_print (res)
http_response_send (res, http_receive)
http_response_delete (res)
```

---

**http_response_json_error_send ()** creates and sends a HTTP json error response with the defined status code & message

``` python
http_response_json_error_send (
	c_void_p,			# reference to a HttpReceive instance
	http_status,		# the response's status code
	c_char_p			# the JSON "your error message" string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_json_error_send (
	http_receive, HTTP_STATUS_BAD_REQUEST, "Missing values!".encode ('utf-8')
)
```

---

**http_response_json_key_value ()** creates a HTTP response with the defined status code and a data (body) with a json meesage of type { key: value } ready to be sent

``` python
http_response_json_key_value (
	http_status,		# the response's status code
	c_char_p,			# the JSON message "key" string value
	c_char_p			# the JSON message "value" string value
) -> c_void_p
```

**Returns** a new HTTP response instance

---

**http_response_json_key_value_send ()** creates and sends a HTTP custom json response with the defined status code & key-value

``` python
http_response_json_key_value_send (
	c_void_p,			# reference to a HttpReceive instance
	http_status,		# the response's status code
	c_char_p,			# the JSON message "key" string value
	c_char_p			# the JSON message "value" string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

**Example**
``` python
http_response_json_key_value_send (
	http_receive, HTTP_STATUS_OK,
	"key".encode ('utf-8'), "value".encode ('utf-8')
)
```
