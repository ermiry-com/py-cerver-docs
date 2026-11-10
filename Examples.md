# Examples

The examples of the repository are in `examples/`, by topic. Each one runs with `python3 examples/<folder>/<example>.py` from the root of the repository, reads its port from `PORT` (8080, or 7000 for the client), and has the `curl` commands to try it in its docstring. `tests/e2e/test_examples.py` starts each one, sends it a request and stops it with SIGINT.

| Example | What it shows |
|---|---|
| `basics/quick.py` | The smallest service: a group with two endpoints, run until SIGINT or SIGTERM |
| `basics/configuration.py` | A service configured from environment variables: the address, the threads, the limits, the timeouts and the policies when it is full or drained |
| `basics/static.py` | A directory of static files, and a file sent by a handler |
| `basics/version.py` | The versions of pycerver and of cerver, printed and served |
| `routing/groups.py` | Groups, subgroups, typed params, a catch all param, the answers without a route and the routes tree as JSON |
| `requests/values.py` | The query, the JSON body and the headers, with the typed validators that answer 400 with every error at once |
| `requests/proxied.py` | A service behind a reverse proxy: the address of the client from its trusted proxies, and a request id in the response and the logs |
| `responses/messages.py` | Python values as JSON, the builders of cerver, text, custom headers and the prebuilt errors |
| `responses/transfers.py` | A file, a streamed body, a redirect, an entity tag and gzip |
| `auth/tokens.py` | A login that signs a JWT, and a group whose endpoints require it |
| `auth/custom.py` | A custom authentication (an API key), also for the admin routes and their metrics |
| `uploads/files.py` | Multipart uploads saved in a directory for each request, a validated image, and the files deleted when the request ends |
| `middleware/chain.py` | The middleware of the cerver, of a group and of an endpoint, and CORS for a whitelist |
| `service/main.py` | A complete service: its configuration, its errors, the handler of its routes, its controllers and a periodic job |
| `workers/handlers.py` | A handlers queue: many requests and a single thread that uses a resource that is not thread safe |
| `workers/manager.py` | Background jobs in a worker manager, with priorities |
| `workers/worker.py` | A worker without HTTP that takes its events from a queue |
| `client/server.py`, `client/client.py` | An echo cerver and its client, over TCP or UDP, with reconnection |
| `deployment/` | A compose with nginx in front of `service/main.py` |

## The layout of a service

`examples/service/` is split like a service of production:
- `config.py` reads the configuration from environment variables with their defaults
- `errors.py` creates the error responses once (`HttpPrebuiltResponses`) and sends them
- `controllers.py` takes the job of a request and returns `(error, errors, result)`, without touching the request or the response
- `handler.py` is the handler of every route: it builds the job, runs its controller and sends its result or its error
- `routes.py` registers the groups
- `main.py` creates the cerver, registers everything and runs it with `cerver_run ()`, so `docker stop` (SIGTERM) drains it
