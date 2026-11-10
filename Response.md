# Response

A handler answers its request with the functions of `cerver.http.responses`, all of them with the `http_receive` that it got.

| Module | What it has |
|---|---|
| `cerver.http.responses.json` | Python values as JSON, the JSON builders of cerver, and the prebuilt responses |
| `cerver.http.responses.core` | A response by hand: its status, headers and body, CORS, compile and send; `http_send_response ()` |
| `cerver.http.responses.files` | Files, text and video |
| `cerver.http.responses.stream` | A body without `Content-Length`, written in pieces |
| `cerver.http.responses.redirects` | `redirect ()` |

## JSON

``` python
http_response_json_send (http_receive, HTTP_STATUS_OK, {"items": items})       # any value of json.dumps ()
http_response_json_msg_send (http_receive, HTTP_STATUS_CREATED, b"Created!")   # {"msg": "Created!"}
http_response_json_error_send (http_receive, HTTP_STATUS_NOT_FOUND, b"Not found!")  # {"error": "Not found!"}
http_response_json_key_value_send (http_receive, HTTP_STATUS_OK, b"id", b"123")
```

The `*_send` builders of cerver take `bytes` and create, send and release their response in a single call. `http_send_response (http_receive, status, body, content_type=...)` sends a `dict` or a `list` as JSON, and a `str` or `bytes` with the content type that it gets.

## Prebuilt responses

The errors of a service can be created once, before it starts, and sent by every handler without creating them again:

``` python
errors = HttpPrebuiltResponses ()
errors.add ("not_found", HTTP_STATUS_NOT_FOUND, {"error": "Not found!"})
errors.add ("bad_request", HTTP_STATUS_BAD_REQUEST, {"error": "Bad request!"})

# in a handler
errors.send ("not_found", http_receive)

# after the service stopped
errors.delete ()
```

`http_response_json_create (status, value)` creates a single compiled response with a copy of its JSON, and `http_response_prebuilt_send (response, http_receive)` sends it without releasing it. A response of `http_response_create_json ()` only references its body, so its JSON has to outlive it.

## A response by hand

``` python
response = http_response_get ()
http_response_set_status (response, HTTP_STATUS_OK)
http_response_add_content_type_header (response, HTTP_CONTENT_TYPE_TXT)
http_response_add_custom_header_value (response, HTTP_HEADER_CACHE_CONTROL, "no-store")
http_response_set_data (response, b"plain text", len (b"plain text"))
http_response_compile (response)
http_response_send (response, http_receive)
http_response_return (response)
```

`http_response_get ()` takes a response of the pool and `http_response_return ()` gives it back; `http_response_create ()` / `http_response_delete ()` create and release one outside of the pool. `http_response_add_whitelist_cors_header_from_request ()` adds `Access-Control-Allow-Origin` for an origin of the whitelist.

## Files, streams and redirects

``` python
http_response_send_file (http_receive, HTTP_STATUS_OK, b"/data/report.pdf")   # with sendfile () and its content type

http_response_stream_begin (http_receive, response)          # the status and headers, without Content-Length
http_response_stream_write (http_receive, data, len (data))  # chunked to an HTTP/1.1 client
http_response_stream_end (http_receive)

redirect (http_receive, HTTP_STATUS_SEE_OTHER, "/api/catalog")
```

`http_response_handle_video ()` sends a video with byte ranges. An endpoint with `etag=True` or `compression=True` (see [[Route]]) answers 304 or a gzip body by itself.
