# PyCerver Documentation

Ermiry's PyCerver Framework official documentation, for **pycerver 1.0** with **cerver 2.0b-64**.

PyCerver is a pure Python wrapper around the cerver framework. It uses the built-in ctypes module to interface with cerver, and provides Python helpers for the common cerver functionality.

## Installation

1. Compile and install cerver 2.0b-64 or newer (`cerver-install.sh` in the repository does it)
2. Install pycerver

``` bash
pip install pycerver==1.0
```

pycerver loads `libcerver.so` at import time, from:
1. the path in the `CERVER_LIBRARY` environment variable;
2. `/usr/local/lib/libcerver.so`;
3. the paths of the system linker (`ctypes.util.find_library`).

When none of them loads, the import raises `cerver.lib.CerverLibraryError`.

## Packages

| Package | Page |
|---|---|
| `cerver` (`cerver.core`) | [[Cerver]] |
| `cerver.http` | [[HTTP]], [[Route]], [[Request]], [[Response]], [[Validation]] |
| `cerver.threads` | [[Threads]] |
| `cerver.network` | [[Network]] |
| `cerver.client` | [[Client]] |
| `cerver.utils`, `cerver.types`, `cerver.version` | [[Utils]] |

Every package re-exports the names of its modules and has an explicit `__all__`. Every binding and helper has its docstring, so `help (cerver.http.http_route_create)` shows its arguments and its return.

## Two kinds of functions

- **Raw bindings** keep the name and the arguments of the C function. Their pointers are `int` (or `c_void_p`), their strings are `bytes`, and a callback has to be a `CFUNCTYPE` object, like `HttpHandler (function)` or a function decorated with `@ctypes.CFUNCTYPE (...)`: a plain Python function raises `ctypes.ArgumentError`.
- **Helpers** take Python values (`str`, `dict`, plain functions) and keep the callbacks alive for as long as the process lives: `http_group`, `HttpGroup`, `http_handler`, `http_response_json_send`, the `*_string` getters of a request, the typed validators, `cerver_event_on`, `Worker`, `WorkerManager` and `cerver_run` among others.

See [[Quickstart]] for a first service, [[Examples]] for the examples of the repository, and [[Migration]] for the changes from 0.9.8.
