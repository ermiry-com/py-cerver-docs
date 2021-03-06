# HTTP Web Example

1. Import the necessary dependencies

``` python
# # provides the ability to handle signals to stop the application
import signal
import sys      # to use sys.exit () to exit the program

# needed to interact with the low level cerver framework
import ctypes

# get access to main and HTTP cerver methods
from cerver import *
from cerver.http import *
```

2. Create a global **Cerver** instance reference

``` python
# define a global reference to the cerver instance
web_service = None
```

3. Define an **end** method to be used as the callback when catching signals. We can use this method to display the cerver's stats and to correctly dispose all the memory that has been allocated by our application

``` python
def end (signum, frame):
  # prints the HTTP cerver stats
  # like how many requests were handled by each route
  http_cerver_all_stats_print (http_cerver_get (web_service))

  # correctly disposes HTTP cerver references
  cerver_teardown (web_service)

  # correctly disposes cerver internal global values
  # should be called only once at the very end of the program
  cerver_end ()

  # exit the application with a custom message
  sys.exit ("Done!")
```

<!-- TODO: -->
4. Create handler methods for different endpoints

``` python

```

5. Define a start method where the **Cerver** instance will be created and its configuration will be set before starting it

``` python
def start ():
  # tell Python to use the global reference
  global web_service

  # create a new cerver of type CERVER_TYPE_WEB
  # that will bind and listen to port 8080 and 
  # with a connection queue of size 10
  web_service = cerver_create_web (b"web-service", 8080, 10)

  # main configuration
  # set the cerver's thpool number of threads
  # This will enable the ability to handle requests using multiple threads
  cerver_set_thpool_n_threads (web_service, 4)
  cerver_set_handler_type (web_service, CERVER_HANDLER_TYPE_THREADS)

  # enable cerver's reusable address flags
  # to avoid errors when binding to the selected port
  cerver_set_reusable_address_flags (web_service, True)

  # HTTP configuration
  # get a reference to the cerver's internal HTTP structure
  http_cerver = http_cerver_get (web_service)

  # enable admin routes in the HTTP cerver
  http_cerver_enable_admin_routes (http_cerver, True)

  # enables the ability to serve static files from "./examples/public"
  http_cerver_static_path_add (http_cerver, b"./examples/public")

  # GET /
  # create a new HTTP route to handle GET requests at / endpoint
  # using the previously defined main_handler ()
  main_route = http_route_create (
    REQUEST_METHOD_GET, b"/", main_handler
  )

  # register the new main route to the HTTP cerver
  http_cerver_route_register (http_cerver, main_route)

  # GET /test
  # create a new HTTP route to handle GET requests at /test endpoint
  # using the previously defined test_handler ()
  test_route = http_route_create (
    REQUEST_METHOD_GET, b"test", test_handler
  )

  # register the new test route to the HTTP cerver
  http_cerver_route_register (http_cerver, test_route)

  # GET /text
  # create a new HTTP route to handle GET requests at /text endpoint
  # using the previously defined text_handler ()
  text_route = http_route_create (
    REQUEST_METHOD_GET, b"text", text_handler
  )

  # register the new text route to the HTTP cerver
  http_cerver_route_register (http_cerver, text_route)

  # GET /json
  # create a new HTTP route to handle GET requests at /json endpoint
  # using the previously defined json_handler ()
  json_route = http_route_create (
    REQUEST_METHOD_GET, b"json", json_handler
  )

  # register the new json route to the HTTP cerver
  http_cerver_route_register (http_cerver, json_route)

  # GET /hola
  # create a new HTTP route to handle GET requests at /hola endpoint
  # using the previously defined hola_handler ()
  hola_route = http_route_create (
    REQUEST_METHOD_GET, b"hola", hola_handler
  )

  # register the new hola route to the HTTP cerver
  http_cerver_route_register (http_cerver, hola_route)

  # GET /adios
  # create a new HTTP route to handle GET requests at /adios endpoint
  # using the previously defined adios_handler ()
  adios_route = http_route_create (
    REQUEST_METHOD_GET, b"adios", adios_handler
  )

  # register the new adios route to the HTTP cerver
  http_cerver_route_register (http_cerver, adios_route)

  # GET /key
  # create a new HTTP route to handle GET requests at /key endpoint
  # using the previously defined key_handler ()
  key_route = http_route_create (
    REQUEST_METHOD_GET, b"key", key_handler
  )

  # register the new key route to the HTTP cerver
  http_cerver_route_register (http_cerver, key_route)

  # start listening for connections and handling requests
  cerver_start (web_service)
```

6. Define the application's entry point where the **cerver** is initialized and the signals are handled

``` python
if __name__ == "__main__":
  # register the end () method to handle signals
  signal.signal (signal.SIGINT, end)
  signal.signal (signal.SIGTERM, end)
  signal.signal (signal.SIGPIPE, signal.SIG_IGN)

  # initializes global cerver values
  # Should be called only once at the start of the program
  cerver_init ()

  # prints the base cerver framework version information
  cerver_version_print_full ()

  # prints the pycerver wrapper version information
  pycerver_version_print_full ()

  # calls the previously defined start () method
  start ()
```
