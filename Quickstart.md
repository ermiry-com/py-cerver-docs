# Quickstart

1. Import what the service uses

``` python
import sys

from cerver import cerver_init, cerver_end, cerver_run
from cerver.http import HTTP_STATUS_OK, REQUEST_METHOD_GET
from cerver.http import http_cerver_configuration, http_cerver_get, http_group
from cerver.http import http_request_get_named_param_string, http_response_json_send
```

2. Write the handler of the endpoint of the group itself. A handler takes the `http_receive` and the `request`, and sends its response

``` python
def hello (http_receive, request):
	"""GET /api/hello"""
	http_response_json_send (http_receive, HTTP_STATUS_OK, {"msg": "Hello from pycerver!"})
```

3. Create the cerver, its group and the endpoints of the group, and run it

``` python
def main () -> int:
	# the global values of cerver, once at the start of the process
	cerver_init ()

	# a web cerver with a thpool, on port 8080
	service = http_cerver_configuration ("quick", port=8080)
	http_cerver = http_cerver_get (service)

	# GET /api/hello is the endpoint of the group
	api = http_group (http_cerver, "api/hello", REQUEST_METHOD_GET, hello)

	# GET /api/hello/:name
	@api.get ("/:name")
	def hello_name (http_receive, request):
		name = http_request_get_named_param_string (request, "name")
		http_response_json_send (http_receive, HTTP_STATUS_OK, {"msg": f"Hello {name}!"})

	# starts the cerver in its own thread, and waits for SIGINT or SIGTERM
	# to stop it (cerver_shutdown) and release it (cerver_teardown)
	result = cerver_run (service)

	# the global values of cerver, once at the end of the process
	cerver_end ()

	return result

if __name__ == "__main__":
	sys.exit (main ())
```

4. Call it

``` bash
curl http://localhost:8080/api/hello
curl http://localhost:8080/api/hello/world
```

`cerver_run ()` has to be called from the main thread: it handles the signals in Python and stops the cerver from there. A cerver started with `cerver_start ()` in the main thread only stops when a signal interrupts its `accept ()`, and a signal that arrives while it starts is lost.

The handlers run in the threads of the thpool of the cerver, so what they share has to be thread safe. See [[Examples]] for the patterns of a complete service.
