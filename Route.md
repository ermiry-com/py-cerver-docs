# Route

The routes of an HTTP cerver are a tree of groups and endpoints, compiled when the cerver starts.

| Module | What it has |
|---|---|
| `cerver.http.decorators` | `http_group`, `HttpGroup`, `http_handler`, `http_endpoint_configure` and the middleware wrappers |
| `cerver.http.tree` | Groups and endpoints: their creation and their settings, and the tree |
| `cerver.http.routes` | The routes of the previous API (`http_route_create`, `http_route_child_add`), the handler types, modifiers and auth types |
| `cerver.http.middleware` | `HttpBeforeMiddleware`, `HttpAfterMiddleware` and their results |
| `cerver.http.validator` | The validators of the params, like `:id<objectid>` |

## Groups and decorators

``` python
items = http_group (http_cerver, "api/items", REQUEST_METHOD_GET, items_list)

@items.get ("/:id<objectid>")
def item_get (http_receive, request):
	item_id = http_request_get_named_param_string (request, "id")
	...

@items.post ("/", max_body_size=1 << 16)
def item_create (http_receive, request):
	...

archived = items.subgroup ("archived")

@archived.delete ("/:number<int>", auth=HTTP_ROUTE_AUTH_TYPE_BEARER)
def archived_delete (http_receive, request):
	...
```

- `http_group (http_cerver, prefix, method, handler)` creates a top level group; with a method and a handler, the group itself is an endpoint (`GET /api/items` above). `HttpGroup.create ()` does the same, and `HttpGroup (group)` wraps a group that already exists (its handle is `.group`)
- `subgroup (prefix, method, handler)` creates a group inside it, which inherits its settings
- the decorators are `get`, `head`, `post`, `put`, `patch`, `delete` and `options`, and `route (method, path)` for any method; each one returns the `HttpHandler` that it registered
- `add (method, path, handler)` and `root (method, handler)` register a function without a decorator
- a handler is a plain Python function `(http_receive, request)`: the group keeps its `HttpHandler` for as long as the process lives

### Paths

- `:name` is a param, read with `http_request_get_named_param_string (request, "name")` or by its position with `http_request_get_param_string (request, 0)`
- `:name<validator>` only matches a valid value: the built-in validators are `objectid`, `int` and `uuid`, and `http_cerver_register_param_validator ()` registers another one (an `HttpParamValidate` that the caller keeps)
- `*name` at the end catches the rest of the path, like `/*path`
- a path that does not match any route gets the not found route of the cerver, and a path that exists with other methods gets 405 with `Allow` when `http_cerver_set_method_not_allowed ()` is on (see [[HTTP]])

### Settings

Every decorator, `add`, `root` and `http_endpoint_configure (endpoint, ...)` take the same settings. A setting that is not given is inherited from the groups of the endpoint:

| Setting | What it does |
|---|---|
| `modifier` | `HTTP_ROUTE_MODIFIER_MULTI_PART` to receive multipart bodies, `HTTP_ROUTE_MODIFIER_WEB_SOCKET` |
| `auth` | `HTTP_ROUTE_AUTH_TYPE_BEARER` to require a JWT, `HTTP_ROUTE_AUTH_TYPE_CUSTOM` for an authentication handler |
| `authentication_with_body` | Authenticate after the body was received, for a custom authentication that reads it |
| `max_body_size` | The biggest body in bytes |
| `cors` | Add `Access-Control-Allow-Origin` for the origins of the whitelist |
| `etag` | A strong `ETag`, and 304 when the client has it |
| `compression` | gzip when the client accepts it |
| `before`, `after` | Python functions that run before or after the handler |

A group is configured with `http_group_set_auth`, `http_group_set_authentication_handler`, `http_group_set_decode_data_into_json`, `http_group_set_cors`, `http_group_set_etag`, `http_group_set_compression`, `http_group_set_max_body_size` and `http_group_set_custom_data`, on its `.group`.

## Middleware

``` python
def maintenance (http_receive, request) -> int:
	if (http_request_get_custom_header_string (request, "X-Maintenance")):
		http_response_json_error_send (http_receive, HTTP_STATUS_SERVICE_UNAVAILABLE, b"In maintenance!")
		return HTTP_MIDDLEWARE_RESULT_STOP

	return HTTP_MIDDLEWARE_RESULT_CONTINUE

orders.before (maintenance)                                         # every endpoint of the group
http_cerver_add_before (http_cerver, http_before_middleware (timer))  # every endpoint of the cerver
```

A before middleware returns `HTTP_MIDDLEWARE_RESULT_CONTINUE` to run the handler, or `HTTP_MIDDLEWARE_RESULT_STOP` when it already answered. An after middleware runs after the handler and returns nothing. `group.before ()` and `group.after ()` keep their functions; the raw `http_cerver_add_*`, `http_group_add_*` and `http_endpoint_add_*` take an `HttpBeforeMiddleware` / `HttpAfterMiddleware` that the caller keeps (`http_before_middleware ()` and `http_after_middleware ()` keep it).

## Custom authentication

``` python
AUTHENTICATED = 0
REJECTED = 1

def api_key (http_receive, request) -> int:
	key = http_request_get_custom_header_string (request, "X-Api-Key") or ""
	return AUTHENTICATED if (hmac.compare_digest (key, API_KEY)) else REJECTED

authentication = callback_wrap (api_key, HttpAuthenticationHandler)

http_group_set_auth (reports.group, HTTP_ROUTE_AUTH_TYPE_CUSTOM)
http_group_set_authentication_handler (reports.group, authentication)
```

The handler returns 0 to accept the request, and any other value to answer 401. `callback_wrap ()` of `cerver.callbacks` creates the `CFUNCTYPE` object and keeps it; the same one can be the authentication of the admin routes.

## The previous API

`http_route_create (method, path, handler)`, `http_route_child_add ()` and `http_cerver_route_register ()` still work, with an `HttpHandler` that the caller keeps. A cerver can have routes and groups at the same time.
