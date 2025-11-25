# Migration

The breaking changes of each version are in [incompatibilities.md](https://github.com/ermiry-com/py-cerver/blob/master/incompatibilities.md) of the repository. This page has the steps to move a service from 0.9.8 to 1.0.

## 1. cerver

pycerver 1.0 needs `libcerver.so` 2.0b-64 or newer. In a Dockerfile, `CERVER_VERSION=2.0b-64`, and `pycerver==1.0`.

## 2. Imports

The names imported from the packages (`from cerver import ...`, `from cerver.http import ...`, `from cerver.threads import ...`, `from cerver.utils import ...`) keep working. The imports of the old modules move:

| 0.9.8 | 1.0 |
|---|---|
| `cerver.cerver` | `cerver` (or `cerver.core.cerver`) |
| `cerver.http.http` | `cerver.http` (or `cerver.http.cerver.*`) |
| `cerver.http.request` | `cerver.http` (or `cerver.http.requests.*`) |
| `cerver.http.response` | `cerver.http` (or `cerver.http.responses.*`) |
| `cerver.http.validation` | `cerver.http` (or `cerver.http.validation.*`) |
| `cerver.http.route` | `cerver.http.routes` |
| `cerver.http.alg` | `cerver.http.algorithms` |
| `cerver.threads.job` | `cerver.threads.jobs` |

A module does not leak what it imports anymore: `signal`, `json` or `ctypes` have to be imported where they are used.

## 3. Names

| 0.9.8 | 1.0 |
|---|---|
| `http_status` | `HttpStatus` |
| `http_header` | `HttpHeaderType` |
| `jwt_alg_t` | `JwtAlgorithm` |
| `http_response_add_custom_header_format` | `http_response_add_custom_header_variadic` |

## 4. Callbacks

A callback that is passed to a raw binding has to be a `CFUNCTYPE` object: a function decorated with `@ctypes.CFUNCTYPE (...)` or `@HttpHandler`, or the result of `callback_wrap (function, Type)`. A plain Python function raises `ctypes.ArgumentError`. The helpers (`http_group`, `HttpGroup`, `http_handler`, `cerver_event_on`, `client_event_on`, `Worker`) take plain functions.

## 5. Behavior

- `Worker` has to be released with `delete ()`
- `http_jwt_create ()` raises `TypeError` for a value that is not a `str`, an `int` or a `bool`
- a body number of another type gets `"Field <name> is invalid."` instead of `"... is required."`, and `validate_query_int_actual_value ()` writes `"Failed to validate field <name>."`
- `validate_mparts_optional_file_is_image ()` never writes an error

## 6. Optional: the helpers of 1.0

The helpers replace code that services repeated:

| Before | 1.0 |
|---|---|
| `value.contents.str.decode ("utf-8")` of a getter | `http_request_get_*_string (request, ...)` |
| A copy of `string_decode ()` | `string_text (value)` |
| The cast of the decoded JWT | `http_request_get_decoded_json (request)` |
| A typed validator of the service | `validate_query_*` / `validate_body_*` |
| `signal.signal ()` with `cerver_start ()` in the main thread | `cerver_run (cerver)` |
