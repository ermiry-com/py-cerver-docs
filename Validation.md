# Validation

The validators read a value of a request and write its error in the `errors` of the request, a `dict` of the name of each field and its message, so a handler answers 400 with every error at once:

``` python
errors = {}
body = http_request_get_body_json (request) or {}

order = {
	"product": validate_body_string (body, "product", errors, max_len=64),
	"quantity": validate_body_int (body, "quantity", errors, minimum=1, maximum=100),
	"date": validate_body_date (body, "date", errors),
	"gift": validate_body_bool (body, "gift", errors, required=False, default=False)
}

if (errors):
	http_response_json_send (http_receive, HTTP_STATUS_BAD_REQUEST, {"errors": errors})
```

| Module | What it has |
|---|---|
| `cerver.http.validation.typed` | The typed validators of a query and of a JSON body |
| `cerver.http.validation.query` | The validators of the previous versions for a query |
| `cerver.http.validation.body` | The validators of the previous versions for a JSON body |
| `cerver.http.validation.multipart` | The values and files of a multipart request |
| `cerver.http.validation.image` | Image files (png or jpeg) |
| `cerver.http.validation.common` | The messages, `validation_decode` and `validation_error` |

## Typed validators

Each one has a query version (`validate_query_*`, with the values of `http_request_get_query_params ()`) and a body version (`validate_body_*`, with the `dict` of the body):

| Type | Limits | Value |
|---|---|---|
| `string` | `min_len`, `max_len` | `str` |
| `int` | `minimum`, `maximum` | `int` (a JSON `5.0` is `5`, a bool is never a number) |
| `float` | `minimum`, `maximum` | `float` |
| `bool` | | `bool` (`true`, `yes`, `1`, `false`, ... in a query) |
| `choice` | `choices` (a positional argument) | One of the choices |
| `list` | `item` (a function that converts each item), `min_items`, `max_items` | `list` (`a,b,c` in a query) |
| `date` | `minimum`, `maximum` | `datetime`, from an ISO 8601 text |

Every one also takes `required` (`True` by default) and `default`, which an optional value that is missing returns without an error. The base `validate_string`, `validate_int`, ... take the value itself, for a value that comes from somewhere else.

## Messages

| Constant | Message |
|---|---|
| `VALIDATION_REQUIRED` | `Field <name> is required.` |
| `VALIDATION_INVALID` | `Field <name> is invalid.` |
| `VALIDATION_LENGTH` | `Field <name> must be between <minimum> and <maximum> characters long.` |
| `VALIDATION_RANGE` | `Field <name> must be between <minimum> and <maximum>.` |
| `VALIDATION_CHOICES` | `Field <name> must be one of <choices>.` |
| `VALIDATION_ITEMS` | `Field <name> must have between <minimum> and <maximum> items.` |
| `VALIDATION_FAILED` | `Failed to validate field <name>.` |
| `VALIDATION_FILE_MISSING`, `VALIDATION_NOT_FILE`, `VALIDATION_NOT_IMAGE` | `File <name> is missing.`, `Field <name> is not a file.`, `File <name> is not png or jpeg.` |

A value that is missing gets the required message, and a value that is present but of another type gets the invalid one. `validation_error (errors, field, message, **values)` writes a message of the table with its values.

## Multipart

``` python
name = validate_mparts_value (request, "name", 1, 64, errors)
image = validate_mparts_file_is_image (request, "image", errors)    # its filenames with its "type"
avatar = validate_mparts_optional_file_is_image (request, "avatar")  # None without an error
```

`validate_mparts_exists`, `validate_mparts_int`, `validate_mparts_float`, `validate_mparts_bool` and their `*_with_default` versions read the values of the parts; `validate_mparts_file`, `validate_mparts_file_exists` and `validate_mparts_saved_file_exists` the files.

## The params of a route

The params are validated by the route itself, with `:name<objectid>`, `:name<int>`, `:name<uuid>` or a registered validator (see [[Route]]): a value that is not valid does not match the route.
