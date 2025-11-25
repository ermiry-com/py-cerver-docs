# Request

The handler of an endpoint gets the `request`, and reads it with the functions of `cerver.http.requests`.

| Module | What it has |
|---|---|
| `cerver.http.requests.values` | The values of a request as Python text (`str | None`) |
| `cerver.http.requests.getters` | The method, the url, the headers, the id, the client ip, the content type, the decoded data and the custom data |
| `cerver.http.requests.params` | The params of the route |
| `cerver.http.requests.query` | The query and its values |
| `cerver.http.requests.body` | The body, and the body as JSON |
| `cerver.http.requests.multipart` | The parts and files of a multipart request |
| `cerver.http.requests.methods` | `REQUEST_METHOD_*` and `http_request_method_str ()` |
| `cerver.http.multipart` | The functions of a single part (`MultiPart`) |

## Values as Python text

The raw getters return the `String` of cerver (see [[Utils]]); the helpers of `values` read it, with its length, and return a `str`, or `None` when the value is missing or is not UTF-8:

| Helper | Value |
|---|---|
| `http_request_get_url_string (request)` | The path, without the query |
| `http_request_get_query_string (request)` | The raw query |
| `http_request_get_param_string (request, index)` | A param of the route by its position, percent-decoded |
| `http_request_get_named_param_string (request, name)` | A param of the route by its name |
| `http_request_get_header_string (request, HTTP_HEADER_USER_AGENT)` | A header that cerver knows, by its type |
| `http_request_get_custom_header_string (request, "X-Custom")` | Any other header, by its name in any case |
| `http_request_get_query_value_string (request, "page")` | A value of the query |
| `http_request_get_body_string (request)` | The body as text |
| `http_request_get_id_string (request)` | The id of the request (see request ids in [[HTTP]]) |
| `http_request_get_client_ip_string (request)` | The address of the client, from the trusted proxies when there are |
| `http_request_get_decoded_string (request)` / `http_request_get_decoded_json (request)` | The decoded data of the JWT, as text or as a `dict` |
| `http_request_content_type_is_multi_part (request)` | `True` for a `multipart/form-data` body |

## Params

`http_get_params_dict (request)` returns every param by its name, and `http_get_params_list (request)` in the order of the route. `http_request_get_n_params ()` and `http_request_get_param_at_idx ()` are the raw ones.

## Query

``` python
query = http_request_get_query_params (request)

text = http_request_get_query_value (query, "text")                 # str | None
page = http_request_get_int_query_value (query, "page")             # int | None, None if it is not a number
ratio = http_request_get_float_query_value (query, "ratio")
active = http_request_get_bool_query_value (query, "active")        # "true", "yes", "1", "false", ...
```

The typed validators of [[Validation]] read the query and write the errors of the request.

## Body

`http_request_get_body_json (request)` returns the body as a `dict`, or `None` when there is no body or it is not a JSON object. `http_request_get_body_string ()` returns the text, and `http_request_get_body ()` the raw `String`.

## Multipart

An endpoint with `modifier=HTTP_ROUTE_MODIFIER_MULTI_PART` receives multipart bodies; the files are saved in the uploads path of the cerver (see [[HTTP]]):

``` python
image = http_request_multi_parts_get_file (request, "image")
# {"original": "photo.png", "saved": "/uploads/1/photo.png", "generated": ...}, None without the part

name = http_request_multi_parts_get_value (request, b"name")      # bytes | None, the value of a part that is not a file

for mpart in http_request_multi_parts (request):
	http_multi_part_print (mpart)
```

The files are deleted when the request ends when `http_cerver_set_uploads_delete_when_done ()` is on, unless the handler keeps them with `http_request_multi_part_keep_files ()`.

## Custom data

`http_request_set_custom_data ()` and `http_request_get_custom_data ()` keep a pointer in the request, for a middleware that passes a value to the handler; a Python value is kept in a `dict` by request instead (see `examples/middleware/chain.py`).
