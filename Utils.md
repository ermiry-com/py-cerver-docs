# Utils

| Module | What it has |
|---|---|
| `cerver.utils.log` | The log of cerver: its output, path, time and quiet configuration, and the functions that write to it |
| `cerver.utils.utils` | `printf` and `strtobool` |
| `cerver.types.string` | The `String` of cerver and its readers |
| `cerver.version` | The versions of pycerver and of cerver |
| `cerver.lib` | The library that was loaded, `lib_function` and the conversions of C strings |
| `cerver.callbacks` | `callback_wrap`, `callback_keep` and `callback_release` |

## Log

``` python
cerver_log_success ("The service is ready")
cerver_log_error (f"Failed to read {path}")
cerver_log_both (LOG_TYPE_NONE, LOG_TYPE_NONE, b"Version: %s", version)
cerver_log (LOG_TYPE_WARNING, LOG_TYPE_HTTP, "Slow request")
```

The functions of cerver that write a message are printf functions, and these wrappers always pass the message as the argument of a `"%s"` format, so a `%` in a message (like one that comes from a client) is written as it is. A message with arguments is formatted in Python first (`message % arguments`). They take `str` or `bytes`. The C functions are `cerver_log_*_variadic`, for a caller that needs a real printf format.

The log is configured with `cerver_log_set_output_type` (`LOG_OUTPUT_TYPE_STD`, `_FILE` or `_BOTH`), `cerver_log_set_path`, `cerver_log_set_time_config` (`LOG_TIME_TYPE_TIME`, `_DATE` or `_BOTH`), `cerver_log_set_local_time` and `cerver_log_set_quiet`, before `cerver_init ()`.

## Strings

The raw getters of cerver return a `String *`. Its readers use its length, so a NUL byte is kept:

``` python
string_bytes (value)        # bytes | None
string_text (value)         # str | None, None when it is not UTF-8
string_take_text (value)    # the text of a String that the caller owns, and releases it
```

The `*_string` helpers of a request (see [[Request]]) do it for each value.

## Versions

`version` is a `dict` with the `id`, `name`, `date`, `time`, `author` and the `cerver` version that pycerver binds, and `cerver.__version__` is its id. `pycerver_version_print_full ()` and `cerver_version_print_full ()` print them.

## Library

`cerver.lib.lib` is the loaded `CDLL`, and `lib_functions` has every function that pycerver binds, with its `argtypes`, `restype` and docstring. `lib_function (name, argtypes, restype, doc)` binds a function of the library that pycerver does not.

## Callbacks

`callback_wrap (function, CallbackType)` returns a `CFUNCTYPE` object of a plain Python function (or the same object when it already is one) and keeps it for as long as the process lives, so cerver never calls a released thunk. `callback_release ()` drops it when cerver no longer has it.
