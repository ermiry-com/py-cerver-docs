# HTTP

**CatchAllHandler** = CFUNCTYPE (None, c_void_p, c_void_p) \
**NotFoundHandler** = CFUNCTYPE (None, c_void_p, c_void_p) \
**UploadsFilenameGenerator** = CFUNCTYPE (None, c_void_p, c_char_p, c_char_p) \
**UploadsDirnameGenerator** = CFUNCTYPE (POINTER (String), c_void_p) \
**HttpDeleteCustom** = CFUNCTYPE (None, c_void_p)

---

**http_cerver_get ()** gets the HttpCerver reference in the Cerver instance

``` python
http_cerver_get (
    c_void_p    # reference to a Cerver instance
) -> c_void_p
```

**Returns** a reference to a HttpCerver instance

---

**http_cerver_configuration ()** creates a HTTP cerver with custom configuration

``` python
http_cerver_configuration (
    name: str,      # the HTTP service's name
    port=8080,      # port where service will be exposed
    connection_queue=10,
	buffer_size=4096,
    n_threads=4,
	reusable_address_flags=True
) -> c_void_p
```

**Returns** a reference to the created Cerver instance

## Public

**http_static_path_set_auth ()** sets authentication requirenments for a whole static path

``` python
http_static_path_set_auth (
    c_void_p,           # reference to a HttpStaticPath instance
    HttpRouteAuthType   # the HttpRouteAuthType value to be used
) -> None
```

---

**http_cerver_static_path_add ()** adds a new static path where static files can be served upon request. It is recomened to set absoulute paths

``` python
http_cerver_static_path_add (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p    # the path to be added as a string reference
) -> c_void_p
```

**Returns** a new HttpStaticPath instance

---

**http_receive_public_path_remove ()** removes a path from the served public paths

``` python
http_receive_public_path_remove (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p    # the path to be removed as a string reference
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_create_route ()** creates and registers a new HTTP route

``` python
http_create_route (
    http_cerver: c_void_p,
    request_method: RequestMethod,
	route_path: str,
    handler: HttpHandler,
	auth_method=HTTP_ROUTE_AUTH_TYPE_NONE
) -> c_void_p
```

**Returns** a reference to the registered HttpRoute instance

---

**http_create_child_route ()** creates and registers a child route to a parent route. The parent's path will be added to the child's path

``` python
http_create_child_route (
    parent: c_void_p,
    request_method: RequestMethod,
	route_path: str,
    handler: HttpHandler,
	auth_method=HTTP_ROUTE_AUTH_TYPE_NONE
) -> c_void_p
```

**Returns** reference to the child HttpRoute instance

## Routes

**http_cerver_set_main_route ()** sets the HTTP cerver's main route (top level route) used to enable admin routes. If no route has been set, the first top level route will be used

``` python
http_cerver_set_main_route (
    c_void_p,   # reference to a HttpCerver instance
    c_void_p	# reference to a HttpRoute instance
) -> None
```

---

**http_cerver_route_register ()** registers a new HTTP route to the HTTP cerver

``` python
http_cerver_route_register (
    c_void_p,   # reference to a HttpCerver instance
    c_void_p    # reference to a HttpRoute instance
) -> None
```

---

**http_cerver_set_catch_all_route ()** sets a route to catch any requet that didn't match any registered route

``` python
http_cerver_set_catch_all_route (
    c_void_p,           # reference to a HttpCerver instance
    CatchAllHandler     # the callback of type CatchAllHandler to be used
) -> None
```

---

**http_cerver_set_not_found_handler ()** enables the use of the default not found handler which returns 404 status on no matching routes. This option is disabled by default as the catch all handler is used instead

``` python
http_cerver_set_not_found_handler (
    c_void_p    # reference to a HttpCerver instance
) -> None
```

---

**http_cerver_set_not_found_route ()** sets a custom handler to be executed on no matching routes that should return status 404

``` python
http_cerver_set_not_found_route (
    c_void_p,           # reference to a HttpCerver instance
    NotFoundHandler     # the callback of type NotFoundHandler to be used
) -> None
```

## Uploads

**http_cerver_set_uploads_path ()** sets the default uploads path where any multipart file request will be saved. This method will replace the previous value with the new one

``` python
http_cerver_set_uploads_path (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p	# the path to be used as a string reference
) -> None
```

---

**http_cerver_generate_uploads_path ()** works like http_cerver_set_uploads_path () but can generate a custom path on the fly using variable arguments

``` python
http_cerver_generate_uploads_path (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p	# the format to be used
) -> None
```

---

**http_cerver_set_uploads_file_mode ()** sets the mode_t to be used when creating uploads files. The default value is HTTP_CERVER_DEFAULT_UPLOADS_FILE_MODE

``` python
http_cerver_set_uploads_file_mode (
    c_void_p,   # reference to a HttpCerver instance
    c_uint      # the mode_t to be used when creating uploads files
) -> None
```

---

**http_cerver_set_uploads_filename_generator ()** sets a method that should generate a c string to be used to save each incoming file of any multipart request. The new filename should be placed in generated_filename with a max size of HTTP_MULTI_PART_GENERATED_FILENAME_LEN

``` python
http_cerver_set_uploads_filename_generator (
    c_void_p,                   # reference to a HttpCerver instance
    UploadsFilenameGenerator    # the callback to be used
) -> None
```

---

**http_cerver_set_default_uploads_filename_generator ()** sets the HTTP cerver's uploads filename generator to be http_cerver_default_uploads_filename_generator ()

``` python
http_cerver_set_default_uploads_filename_generator (
    c_void_p    # reference to a HttpCerver instance
) -> None
```

---

**http_cerver_set_uploads_dir_mode ()** sets the mode_t value to be used when creating uploads dirs. The default value is HTTP_CERVER_DEFAULT_UPLOADS_DIR_MODE

``` python
http_cerver_set_uploads_dir_mode (
    c_void_p,   # reference to a HttpCerver instance
    c_uint      # the mode_t value to be used when creating uploads dirs
) -> None
```

---

**http_cerver_set_uploads_dirname_generator ()** sets a method to be called on every new request that will be used to generate a new directory inside the uploads path to save all the files from each request

``` python
http_cerver_set_uploads_dirname_generator (
    c_void_p,                   # reference to a HttpCerver instance
    UploadsDirnameGenerator	    # the callback to be used
) -> None
```

---

**http_cerver_set_default_uploads_dirname_generator ()** sets the HTTP cerver's uploads dirname generator to be http_cerver_default_uploads_dirname_generator ()

``` python
http_cerver_set_default_uploads_dirname_generator (
    c_void_p    # reference to a HttpCerver instance
) -> None
```

---

**http_cerver_set_uploads_delete_when_done ()** specifies whether uploads are deleted after the requested has ended unless the request files have been explicitly saved using http_request_multi_part_keep_files (). The default value is HTTP_CERVER_DEFAULT_UPLOADS_DELETE

``` python
http_cerver_set_uploads_delete_when_done (
    c_void_p,   # reference to a HttpCerver instance
    c_bool      # the value to enable or disable this option
) -> None
```

## Auth

**http_jwt_get_bearer ()** gets the HttpJwt instance bearer field as a string reference

``` python
http_jwt_get_bearer (
    c_void_p    # reference to a HttpJwt instance
) -> c_char_p
```

**Returns** a constant reference to the HttpJwt bearer field as a string reference

---

**http_jwt_get_bearer_len ()** gets the HttpJwt instance bearer field length value

``` python
http_jwt_get_bearer_len (
    c_void_p    # reference to a HttpJwt instance
) -> c_size_t
```

**Returns** the HttpJwt bearer length value

---

**http_jwt_get_json ()** gets the HttpJwt instance json field as a string reference

``` python
http_jwt_get_json (
    c_void_p    # reference to a HttpJwt instance
) -> c_char_p
```

**Returns** a constant reference to the HttpJwt json field as a string reference

---

**http_jwt_get_json_len ()** gets the HttpJwt instance json field length value

``` python
http_jwt_get_json_len (
    c_void_p    # reference to a HttpJwt instance
) -> c_size_t
```

**Returns** the HttpJwt bearer json value

---

**http_cerver_auth_set_jwt_algorithm ()** sets the jwt algorithm used for encoding & decoding jwt tokens. The default value is JWT_ALG_HS256

``` python
http_cerver_auth_set_jwt_algorithm (
    c_void_p    # reference to a HttpCerver instance
    jwt_alg_t   # the jwt_alg_t value to be used
) -> None
```

---

**http_cerver_auth_set_jwt_priv_key_filename ()** sets the filename from where the jwt private key will be loaded

``` python
http_cerver_auth_set_jwt_priv_key_filename (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p	# path to the private key to be used
) -> None
```

---

**http_cerver_auth_set_jwt_pub_key_filename ()** sets the filename from where the jwt public key will be loaded

``` python
http_cerver_auth_set_jwt_pub_key_filename (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p    # path to the public key to be used
) -> None
```

---

**http_cerver_auth_jwt_new ()** creates a new HttpJwt instance

``` python
http_cerver_auth_jwt_new () -> c_void_p
```

**Returns** a new HttpJwt instance

---

**http_cerver_auth_jwt_delete ()** correctly disposes a HttpJwt instance

``` python
http_cerver_auth_jwt_delete (
    c_void_p    # reference to a HttpJwt instance
) -> None
```

---

**http_cerver_auth_jwt_add_value ()** adds a new string value to be used to generate the JWT with matching key

``` python
http_cerver_auth_jwt_add_value (
    c_void_p,   # reference to a HttpJwt instance
    c_char_p,   # the key to be used
    c_char_p    # the new value as a string reference
) -> None
```

---

**http_cerver_auth_jwt_add_value_bool ()** adds a new boolean value to be used to generate the JWT with matching key

``` python
http_cerver_auth_jwt_add_value_bool (
    c_void_p,   # reference to a HttpJwt instance
    c_char_p,   # the key to be used
    c_bool      # the new boolean value
) -> None
```

---

**http_cerver_auth_jwt_add_value_int ()** adds a new integer value to be used to generate the JWT with matching key

``` python
http_cerver_auth_jwt_add_value_int (
    c_void_p,   # reference to a HttpJwt instance
    c_char_p,   # the key to be used
    c_int	    # the new integer value
) -> None
```

---

**http_cerver_auth_generate_bearer_jwt_json ()** generates and signs a bearer JWT nd places it inside a json packet

``` python
http_cerver_auth_generate_bearer_jwt_json (
    c_void_p,   # reference to a HttpCerver instance
    c_void_p	# reference to a HttpJwt instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_cerver_auth_generate_bearer_jwt_json_with_value ()** works as http_cerver_auth_generate_bearer_jwt_json () but with the ability to add an extra string value to the generated json

``` python
http_cerver_auth_generate_bearer_jwt_json_with_value (
    c_void_p,   # reference to a HttpCerver instance
    c_void_p,   # reference to a HttpJwt instance
    c_char_p,   # the JSON extra "key" string value
    c_char_p	# the JSON extra "value" string value
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_cerver_auth_configuration ()** configurates the HTTP service's auth JWT algorithm and keys

``` python
http_cerver_auth_configuration (
    http_cerver: c_void_p,
    jwt_algorithm=JWT_ALG_NONE,
	priv_key_filename=None,
    pub_key_filename=None
) -> None
```

---

**http_jwt_create ()** creates a HttpJwt instance with custom values. The returned HttpJwt instance must be deleted with http_cerver_auth_jwt_delete () to avoid memory leaks

``` python
http_jwt_create (
    values      # the actual Bearer JWT values
) -> c_void_p
```

**Returns** reference to a newly created HttpJwt instance

---

**http_jwt_create_and_send ()** creates and sends a Bearer JWT

``` python
http_jwt_create_and_send (
    http_receive: c_void_p,
	status_code=HTTP_STATUS_OK,
	values={}
) -> None
```

---

**http_jwt_token_decode ()** loads the decoded data from a JWT into a dict

``` python
http_jwt_token_decode (
    request: c_void_p   # reference to a HTTP request instance
) -> c_void_p
```

**Returns** the JWT decoded data as a dict

## Origins

**http_cerver_add_origin_to_whitelist ()** adds a new domain to the HTTP cerver's origins whitelist

``` python
http_cerver_add_origin_to_whitelist (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p    # the domain to be added to the whitelist
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**http_cerver_print_origins_whitelist ()** prints the domains in the origins whitelist

``` python
http_cerver_print_origins_whitelist (
    c_void_p    # reference to a HttpCerver instance
) -> None
```

## Data

**http_cerver_get_custom_data ()** gets the HTTP cerver's custom data. This data can be safely set by the user and accessed at any time

``` python
http_cerver_get_custom_data (
    c_void_p    # reference to a HttpCerver instance
) -> c_void_p
```

**Returns** the reference to the HTTP cerver's custom data

---

**http_cerver_set_custom_data ()** sets the HTTP cerver's custom data

``` python
http_cerver_set_custom_data (
    c_void_p,   # reference to a HttpCerver instance
    c_void_p    # reference to the custom data
) -> None
```

---

**http_cerver_set_delete_custom_data ()** sets a custom method to delete the HTTP cerver's custom data

``` python
http_cerver_set_delete_custom_data (
    c_void_p,           # reference to a HttpCerver instance
    HttpDeleteCustom    # the callback method to delete the custom data  
) -> None
```

---

**http_cerver_set_default_delete_custom_data ()** sets free () as the method to delete the HTTP cerver's custom data

``` python
http_cerver_set_default_delete_custom_data (
    c_void_p    # reference to a HttpCerver instance
) -> None
```

## Responses

**http_cerver_add_responses_header ()** adds a new global responses header. This header will be added to all the responses. If the response has the same header type, it will be used instead of the global header

``` python
http_cerver_add_responses_header (
    c_void_p,       # reference to a HttpCerver instance
    http_header,    # the http_header header to be added
    c_char_p        # the real header value as a C string reference
) -> c_uint8
```

**Returns** 0 on success, 1 on error

## Stats

**http_cerver_all_stats_print ()** prints all HTTP cerver stats, general & by route

``` python
http_cerver_all_stats_print (
    c_void_p    # reference to a HttpCerver instance
) -> None
```

## Admin

**http_cerver_enable_admin_routes ()** enables the ability to have admin routes to fetch cerver's HTTP stats. The initial value is HTTP_CERVER_DEFAULT_ENABLE_ADMIN

``` python
http_cerver_enable_admin_routes (
    c_void_p,   # reference to a HttpCerver instance
    c_bool      # the value to enable or disable this option
) -> None
```

---

**http_cerver_enable_admin_info_route ()** enables the ability to have an admin info route to fetch cerver's information. The initial value is HTTP_CERVER_DEFAULT_ENABLE_ADMIN_INFO

``` python
http_cerver_enable_admin_info_route (
    c_void_p,   # reference to a HttpCerver instance
    c_bool      # the value to enable or disable this option
) -> None
```

---

**http_cerver_enable_admin_head_handlers ()** enables HTTP admin routes to handle HEAD requests. The initial value is HTTP_CERVER_DEFAULT_ENABLE_ADMIN_HEADS

``` python
http_cerver_enable_admin_head_handlers (
    c_void_p,   # reference to a HttpCerver instance
    c_bool      # the value to enable or disable this option
) -> None
```

---

**http_cerver_enable_admin_options_handlers ()** enables HTTP admin routes to handle OPTIONS requests. The initial value is HTTP_CERVER_DEFAULT_ENABLE_ADMIN_OPTIONS

``` python
http_cerver_enable_admin_options_handlers (
    c_void_p,   # reference to a HttpCerver instance
    c_bool      # the value to enable or disable this option
) -> None
```

---

**http_cerver_enable_admin_routes_authentication ()** enables authentication in admin routes. The initial value is HTTP_CERVER_DEFAULT_ENABLE_ADMIN_AUTH

``` python
http_cerver_enable_admin_routes_authentication (
    c_void_p,           # reference to a HttpCerver instance
    HttpRouteAuthType,  # the HTTP route authentication method to be used
) -> None
```

---

**http_cerver_admin_routes_auth_set_decode_data ()** sets the method to be used to decode incoming data from JWT and sets a method to delete it after use. If no delete method is set, data won't be freed

``` python
http_cerver_admin_routes_auth_set_decode_data (
    c_void_p,           # reference to a HttpCerver instance
    HttpDecodeData,     # the callback method used to decode data
    HttpDeleteDecoded   # the callback method used to free decoded data
) -> None
```

---

**http_cerver_admin_routes_auth_decode_to_json ()** works like http_cerver_enable_admin_routes_authentication () but sets a method to decode data from a JWT into a json string

``` python
http_cerver_admin_routes_auth_decode_to_json (
    c_void_p    # reference to a HttpCerver instance
) -> None
```

---

**http_cerver_admin_routes_set_authentication_handler ()** sets a method to be used to handle auth in admin routes. HTTP cerver must had been configured with HTTP_ROUTE_AUTH_TYPE_CUSTOM. Method must return 0 on success and 1 on error

``` python
http_cerver_admin_routes_set_authentication_handler (
    c_void_p                # reference to a HttpCerver instance
    AuthenticationHandler   # the authentication callback to be used
) -> None
```

---

**http_cerver_enable_admin_cors_headers ()** enables CORS headers in admin routes responses. Always uses admin origin's value. If there is no dedicated origin, it will dynamically set the header based on the origins whitelist. The initial value is HTTP_CERVER_DEFAULT_ENABLE_ADMIN_CORS

``` python
http_cerver_enable_admin_cors_headers (
    c_void_p,   # reference to a HttpCerver instance
    c_bool      # the value to enable or disable this option
) -> None
```

---

**http_cerver_admin_set_origin ()** sets the dedicated domain that will be always set in the admin responses CORS headers

``` python
http_cerver_admin_set_origin (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p    # the domain to be used
) -> None
```

---

**http_cerver_register_admin_file_system ()** registers a new file system to be handled when requesting for fs stats

``` python
http_cerver_register_admin_file_system (
    c_void_p,   # reference to a HttpCerver instance
    c_char_p    # path to the fs to handle as a string reference
) -> None
```

## Handler

**http_receive_get_cerver_receive ()** gets the CerverReceive reference from a HttpReceive instance

``` python
http_receive_get_cerver_receive (
    c_void_p    # reference to a HttpReceive instance
) -> c_void_p
```

**Returns** a reference to a CerverReceive instance

**http_receive_get_sock_fd ()** gets the sock fd associated to a HttpReceive instance

``` python
http_receive_get_sock_fd (
    c_void_p    # reference to a HttpReceive instance
) -> c_void_p
```

**Returns** the sock fd value

**http_receive_get_cerver ()** gets the HttpCerver reference from a HttpReceive instance

``` python
http_receive_get_cerver (
    c_void_p    # reference to a HttpReceive instance
) -> c_void_p
```

**Returns** a reference to a HttpCerver instance
