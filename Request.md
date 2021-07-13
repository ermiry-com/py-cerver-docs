# HTTP Request

**RequestMethod**

REQUEST_METHOD_DELETE \
REQUEST_METHOD_GET \
REQUEST_METHOD_HEAD \
REQUEST_METHOD_POST \
REQUEST_METHOD_PUT \
REQUEST_METHOD_CONNECT \
REQUEST_METHOD_OPTIONS \
REQUEST_METHOD_TRACE

**HttpDeleteDecoded** = CFUNCTYPE (None, c_void_p) \
**HttpDeleteCustom** = CFUNCTYPE (None, c_void_p)

---

**http_request_get_method ()** gets the HTTP request's method (GET, POST, ...)

``` python
http_request_get_method (
    c_void_p    # reference to a HTTP request instance
) -> RequestMethod
```

**Returns** a RequestMethod value representing the request's method

---

**http_request_get_url ()** gets the HTTP request's url string value

``` python
http_request_get_url (
    c_void_p    # reference to a HTTP request instance
) -> POINTER (String)
```

**Returns** the request's url as a String reference

---

**http_request_get_query ()** gets the HTTP request's query string value

``` python
http_request_get_query (
    c_void_p    # reference to a HTTP request instance
) -> POINTER (String)
```

**Returns** the request's query as a String reference

---

**http_request_get_query_params ()** gets the HTTP request's query params list

``` python
http_request_get_query_params (
    c_void_p    # reference to a HTTP request instance
) -> c_void_p
```

**Returns** a list with the HTTP request's query params

---

**http_request_get_n_params ()** gets how many query params are in the HTTP request

``` python
http_request_get_n_params (
    c_void_p    # reference to a HTTP request instance
) -> c_uint
```

**Returns** the number of query params in the HTTP request

---

**http_request_get_param_at_idx ()** gets the HTTP request's x query param

``` python
http_request_get_param_at_idx (
    c_void_p,   # reference to a HTTP request instance
    c_uint      # the idx of the param to get
) -> c_uint
```

**Returns** a String reference of the selected param, None if the idx was invalid

---

**http_request_get_header ()** gets the specified HTTP header value from the HTTP request

``` python
http_request_get_header (
    c_void_p        # reference to a HTTP request instance
    http_header     # the header type to get
) -> POINTER (String)
```

**Returns** a String reference to the request's header, None if the request type was not found or is invalid

---

**http_request_get_content_type ()** gets the HTTP request's content type value from the request's headers

``` python
http_request_get_content_type (
    c_void_p    # reference to a HTTP request instance
) -> ContentType
```

**Returns** a ContentType value taken from the request's headers

---

**http_request_get_content_type_string ()** gets the HTTP request's HTTP_HEADER_CONTENT_TYPE String reference

``` python
http_request_get_content_type_string (
    c_void_p    # reference to a HTTP request instance
) -> POINTER (String)
```

**Returns** a String reference taken from the request's header

---

**http_request_content_type_is_json ()** checks if the HTTP request's content type is **application/json**

``` python
http_request_content_type_is_json (
    c_void_p    # reference to a HTTP request instance
) -> c_bool
```

**Returns** true if the request's content type is **application/json**

---

**http_request_get_decoded_data ()** gets the HTTP request's decoded data. This data was created by the HTTP route's decode_data method configured by http_route_set_decode_data () or http_route_set_decode_data_into_json ()

``` python
http_request_get_decoded_data (
    c_void_p    # reference to a HTTP request instance
) -> c_void_p
```

**Returns** a reference to the request's decoded data

---

**http_request_set_decoded_data ()** sets the HTTP request's decoded data

``` python
http_request_set_decoded_data (
    c_void_p,   # reference to a HTTP request instance
    c_void_p    # reference to the decoded data
) -> None
```

---

**http_request_set_delete_decoded_data ()** sets a custom method to delete the HTTP request's decoded data

``` python
http_request_set_delete_decoded_data (
    c_void_p,           # reference to a HTTP request instance
    HttpDeleteDecoded   # the delete decoded data callback
) -> None
```

---

**http_request_set_default_delete_decoded_data ()** sets free () as the method to delete the HTTP request's decoded data

``` python
http_request_set_default_delete_decoded_data (
    c_void_p    # reference to a HTTP request instance
) -> None
```

---

**http_request_get_custom_data ()** gets the HTTP request's custom data. This data can be safely set by the user and accessed at any time

``` python
http_request_get_custom_data (
    c_void_p    # reference to a HTTP request instance
) -> c_void_p
```

**Returns** a reference to the request's custom data

---

**http_request_set_custom_data ()** sets the HTTP request's custom data

``` python
http_request_set_custom_data (
    c_void_p,   # reference to a HTTP request instance
    c_void_p    # reference to the custom data
) -> None
```

---

**http_request_set_delete_custom_data ()** sets a custom method to delete the HTTP request's custom data

``` python
http_request_set_delete_custom_data (
    c_void_p,           # reference to a HTTP request instance
    HttpDeleteCustom    # the delete custom data callback
) -> None
```

---

**http_request_set_default_delete_custom_data ()** sets free () as the method to delete the HTTP request's custom data

``` python
http_request_set_default_delete_custom_data (
    c_void_p    # reference to a HTTP request instance
) -> None
```

---

**http_request_get_current_mpart ()** gets the HTTP request's current multi-part structure

``` python
http_request_get_current_mpart (
    c_void_p    # reference to a HTTP request instance
) -> c_void_p
```

**Returns** reference to the request's current multi-part value

---

**http_request_get_n_files ()** gets the HTTP request's multi-part files count

``` python
http_request_get_n_files (
    c_void_p    # reference to a HTTP request instance
) -> c_uint8
```

**Returns** how many multi-part files were handled in the request

---

**http_request_get_n_values ()** gets the HTTP request's multi-part values count

``` python
http_request_get_n_values (
    c_void_p    # reference to a HTTP request instance
) -> c_uint8
```

**Returns** how many multi-part values were handled by the request

---

**http_request_get_dirname_len ()** gets the HTTP request's multi-part files dirname length

``` python
http_request_get_dirname_len (
    c_void_p    # reference to a HTTP request instance
) -> c_int
```

**Returns** the HTTP request's multi-part files dirname length

---

**http_request_get_dirname ()** gets the HTTP request's multi-part files directory generated by uploads_dirname_generator ()

``` python
http_request_get_dirname (
    c_void_p    # reference to a HTTP request instance
) -> c_char_p
```

**Returns** the HTTP request's multi-part files directory

---

**http_request_set_dirname ()** sets the HTTP request's multi-part files directory

``` python
http_request_set_dirname (
    c_void_p,   # reference to a HTTP request instance
    c_char_p    # the format to be used
) -> None
```

---

**http_request_get_body ()** gets the HTTP request's body

``` python
http_request_get_body (
    c_void_p    # reference to a HTTP request instance
) -> POINTER (String)
```

**Returns** a String reference to the HTTP request's body

---

**http_request_get_body_values ()** gets the HTTP request's body values

``` python
http_request_get_body_values (
    c_void_p    # reference to a HTTP request instance
) -> c_void_p
```

**Returns** a list containing the HTTP request's body values

---

**http_request_get_query_value ()** gets a query param from the HTTP request

``` python
http_request_get_query_value (
    values,     # key-value pairs from x-www-form-urlencoded data or query params
    query_name	# the key used to find a matching value
) -> c_void_p
```

**Returns**

---

**http_request_get_body_json ()** gets the HTTP request's body in a dictionary

``` python
http_request_get_body_json (
    request     # reference to a HTTP request instance
) -> Python Dictionary
```

**Returns**

## Headers

**http_request_headers_print ()** prints the HTTP request's headers values

``` python
http_request_headers_print (
    c_void_p    # reference to a HTTP request instance
) -> None
```

**Returns**

## Query

**http_request_query_params_print ()** prints the request's query params values

``` python
http_request_query_params_print (
    c_void_p    # reference to a HTTP request instance
) -> None
```

---

**http_request_query_params_get_value ()** searches the request's query params values for the specified key

``` python
http_request_query_params_get_value (
    c_void_p,   # reference to a HTTP request instance
    c_char_p    # the key to be used to find a matching value
) -> POINTER (String)
```

**Returns** the matching query param value for the specified key, None if no match or no query params

## Multi-Part

**http_request_multi_parts_get ()** searches the request's multi-parts for one with matching key

``` python
http_request_multi_parts_get (
    c_void_p,   # reference to a HTTP request instance
    c_char_p    # the key to be used to find a matching value
) -> c_void_p
```

**Returns** a reference to the multi-part instance that should not be deleted if found, NULL if not match

---

**http_request_multi_parts_get_value ()** searches the request's multi-parts values for a value with matching key

``` python
http_request_multi_parts_get_value (
    c_void_p,   # reference to a HTTP request instance
    c_char_p    # the key to be used to find a matching value
) -> c_char_p
```

**Returns** a constant C string reference that should not be deleted if found, NULL if not match

---

**http_request_multi_parts_get_filename ()** searches the request's multi-parts values for a filename with matching key

``` python
http_request_multi_parts_get_filename (
    c_void_p,   # reference to a HTTP request instance
    c_char_p    # the key to be used to find a matching value
) -> c_char_p
```

**Returns** a constant c string that should not be deleted if found, None if not match

---

**http_request_multi_parts_get_saved_filename ()** searches the request's multi-parts values for a saved filename with matching key

``` python
http_request_multi_parts_get_saved_filename (
    c_void_p,   # reference to a HTTP request instance
    c_char_p    # the key to be used to find a matching value
) -> c_char_p
```

**Returns** a constant C string reference that should not be deleted if found, None if not match

---

**http_request_multi_parts_iter_start ()** starts the HTTP request's multi-parts internal iterator

``` python
http_request_multi_parts_iter_start (
    c_void_p    # reference to a HTTP request instance
) -> c_bool
```

**Returns** true on success, false on error

---

**http_request_multi_parts_iter_get_next ()** gets the next request's multi-part using the iterator

``` python
http_request_multi_parts_iter_get_next (
    c_void_p    # reference to a HTTP request instance
) -> c_void_p
```

**Returns** NULL if at the end of the list or error

---

**http_request_multi_part_keep_files ()** signals that the files referenced by the request should be kept, so they won't be deleted after the request has ended. Files only get deleted if the HTTP cerver's uploads_delete_when_done attribute is set to TRUE

``` python
http_request_multi_part_keep_files (
    c_void_p    # reference to a HTTP request instance
) -> None
```

---

**http_request_multi_part_discard_files ()** discards all the saved files from the multipart request

``` python
http_request_multi_part_discard_files (
    c_void_p    # reference to a HTTP request instance
) -> None
```

---

**http_request_multi_parts_print ()** prints the request's multi-parts values

``` python
http_request_multi_parts_print (
    c_void_p    # reference to a HTTP request instance
) -> None
```

## Body

**http_request_body_get_value ()** search request's body values for matching value by key

``` python
http_request_body_get_value (
    c_void_p,   # reference to a HTTP request instance
    c_char_p    # the key to be used to find a matching value
) -> POINTER (String)
```

**Returns** a constant String reference that should not be deleted if match, None if not found
