# HTTP

`from cerver.http import ...` has every name of the HTTP subpackage. This page is about the HTTP cerver and its configuration; see [[Route]], [[Request]], [[Response]] and [[Validation]] for the rest.

| Module | What it has |
|---|---|
| `cerver.http.cerver.server` | `http_cerver_configuration`, `http_cerver_get`, static paths, custom data, response headers and stats |
| `cerver.http.cerver.routes` | Route registration, the main, catch all, not found and method not allowed routes, trailing slashes, middleware of every endpoint, the routes tree as JSON |
| `cerver.http.cerver.limits` | The sizes and timeouts of a request |
| `cerver.http.cerver.defaults` | The `Date` header and the request ids |
| `cerver.http.cerver.uploads` | Where and how the files of a multipart request are saved |
| `cerver.http.cerver.auth` | The JWTs of the cerver |
| `cerver.http.cerver.origins` | The CORS whitelist |
| `cerver.http.cerver.admin` | The admin routes (`/cerver/...`) |
| `cerver.http.cerver.receive` | The values of an `http_receive` |
| `cerver.http.cerver.metrics` | The metrics in the text format of Prometheus |
| `cerver.http.proxy`, `cerver.http.compression`, `cerver.http.conditional`, `cerver.http.date` | Trusted proxies, gzip, entity tags and HTTP dates |
| `cerver.http.content`, `cerver.http.headers`, `cerver.http.status`, `cerver.http.origin`, `cerver.http.url`, `cerver.http.utils`, `cerver.http.algorithms` | Content types, header types, status codes, origins, URL encoding, byte ranges and JWT algorithms |

## The cerver of a service

``` python
service = http_cerver_configuration ("orders", port=8080, n_threads=4)
http_cerver = http_cerver_get (service)
```

`http_cerver_configuration ()` creates a web cerver named `"<name>-service"` whose connections are handled by a thpool, with reusable address flags. It takes `port`, `connection_queue`, `buffer_size`, `n_threads` and `reusable_address_flags`, and returns `None` when the cerver can not be created. Every other setting is a setter of [[Cerver]] on `service`, or of this page on `http_cerver`.

## Routes of the cerver

- `http_cerver_set_not_found_route (http_cerver, http_handler (not_found))`: the handler of the requests that no route matched
- `http_cerver_set_method_not_allowed (http_cerver, True)`: answer 405 with `Allow` when the path exists with other methods
- `http_cerver_set_trailing_slash (http_cerver, HTTP_TRAILING_SLASH_REDIRECT)`: what a path with a trailing slash does
- `http_cerver_static_path_add (http_cerver, b"public")`: a directory whose files are served when no endpoint matches
- `http_cerver_add_before` / `http_cerver_add_after`: a middleware of every endpoint (see [[Route]])
- `http_cerver_routes_json (http_cerver)`: the compiled routes tree as a `dict`, and `http_cerver_routes_print` to print it

## Limits

`http_cerver_set_max_body_size`, `http_cerver_set_max_url_size`, `http_cerver_set_max_header_size`, `http_cerver_set_max_headers`, `http_cerver_set_max_multipart_parts`, `http_cerver_set_max_file_size`, `http_cerver_set_headers_timeout` and `http_cerver_set_request_timeout`. A group or an endpoint can have its own `max_body_size`.

## Behind a proxy

``` python
http_cerver_set_trusted_proxies (http_cerver, HTTP_TRUSTED_PROXIES_LIST)
http_cerver_add_trusted_proxy (http_cerver, b"172.16.0.0/12")
http_cerver_set_request_id (http_cerver, True)
```

With trusted proxies, `X-Forwarded-For` and `X-Real-IP` give the address of the client (`http_request_get_client_ip_string ()`) only when the peer is trusted. `HTTP_TRUSTED_PROXIES_PRIVATE` trusts the private networks, and `HTTP_TRUSTED_PROXIES_ALL` every peer. With request ids, every request keeps its valid `X-Request-Id` or gets a new UUID v4, which is echoed in the response (`http_request_get_id_string ()`).

## Compression and entity tags

- `http_cerver_set_compression_min_size` and `http_cerver_set_compression_level` configure gzip; a group or an endpoint enables it with `compression=True`
- an endpoint with `etag=True` answers 304 when the client already has its body; `http_etag_from_data ()` and `http_etag_from_file ()` create a tag by hand

## JWTs

``` python
# HS256: the same secret signs and validates; RS256 / ES256: the private key signs, the public key validates
http_cerver_auth_configuration (http_cerver, JWT_ALG_HS256, "secret.key", "secret.key")

# in a login handler, answers {"token": "Bearer <jwt>"}
http_jwt_create_and_send (http_receive, HTTP_STATUS_OK, {"username": "user", "admin": False})
```

The values of a JWT are `str`, `int` (32 bits) or `bool`, any other type raises `TypeError`. A group or an endpoint with `auth=HTTP_ROUTE_AUTH_TYPE_BEARER` validates the token before its handler, and with `http_group_set_decode_data_into_json ()` its handler reads the values with `http_request_get_decoded_json (request)`. To send a token some other way, `http_jwt_create ()` creates the JWT, `http_cerver_auth_generate_bearer_jwt ()` signs it, `http_jwt_get_bearer_string ()` returns its `"Bearer <jwt>"`, and `http_cerver_auth_jwt_delete ()` releases it.

## Admin routes and metrics

``` python
http_cerver_enable_admin_routes (http_cerver, True)
http_cerver_enable_admin_routes_authentication (http_cerver, HTTP_ROUTE_AUTH_TYPE_BEARER)
```

The admin routes (`/cerver/info`, `/cerver/stats`, `/cerver/metrics`...) take the same authentication types as an endpoint, and a custom authentication handler with `http_cerver_admin_routes_set_authentication_handler ()`. `http_cerver_metrics (http_cerver)` returns the metrics as text, with the thpool and the routes.

## Constants

- **status:** `HTTP_STATUS_OK`, `HTTP_STATUS_CREATED`, `HTTP_STATUS_BAD_REQUEST`, ... with `http_status_string ()`; the type is `HttpStatus`
- **headers:** `HTTP_HEADER_CONTENT_TYPE`, `HTTP_HEADER_AUTHORIZATION`, `HTTP_HEADER_REAL_IP`, ... with `http_header_string ()`; the type is `HttpHeaderType`
- **content types:** `HTTP_CONTENT_TYPE_JSON`, `HTTP_CONTENT_TYPE_HTML`, ... with `http_content_type_by_extension ()` and `http_content_type_mime ()`; the type is `ContentType`
- **JWT algorithms:** `JWT_ALG_HS256`, `JWT_ALG_RS256`, `JWT_ALG_ES256`, ... with `jwt_alg_str ()`; the type is `JwtAlgorithm`
