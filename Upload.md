# HTTP Upload Example

1. Import the necessary dependencies

``` python
# provides the ability to handle signals to stop the application
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

4. Create a handler method for the **GET /test** route and another handler method for the **POST /upload** route

``` python
# GET /test
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def test_handler (http_receive, request):
  # creates a HTTP response with a status code and JSON body
  response = http_response_json_msg (
    HTTP_STATUS_OK, b"Test route works!"
  )

  # print the generated HTTP response
  http_response_print (response)

  # send the HTTP response to the client
  http_response_send (response, http_receive)

  # correctly disposes a HTTP response and all of its data
  http_response_delete (response)

# POST /upload
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def upload_handler (http_receive, request):
  # prints the HTTP request's multi-part values
  http_request_multi_parts_print (request)

  # search a multi-part value by key
  value = http_request_multi_parts_get_value (request, b"value")

  # check to see if the value was found in the request
  if (value):
    # print the value's content
    print (value.contents.str)

  # send back a JSON response back to the client
  http_response_json_msg_send (
    http_receive, HTTP_STATUS_OK, b"Upload works!"
  )
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
  # set the cerver's receive buffer size.
  # This is useful to improve efficiency based on the amount of data
  # that needs to be handled with every request.
  # For example a bigger buffer is better when handling file uploads
  cerver_set_receive_buffer_size (web_service, 4096)

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

  # set the base path where multi-part file uploads will be saved
  http_cerver_set_uploads_path (http_cerver, b"uploads")

  # GET /test
  # create a new HTTP route to handle GET requests at /test endpoint
  # using the previously defined main_handler ()
  test_route = http_route_create (
    REQUEST_METHOD_GET, b"test", test_handler
  )

  # register the new test route to the HTTP cerver
  http_cerver_route_register (http_cerver, test_route)

  # POST /upload
  # create a new HTTP route to handle POST requests at /upload endpoint
  # using the previously defined upload_handler ()
  upload_route = http_route_create (
    REQUEST_METHOD_POST, b"upload", upload_handler
  )

  # configure the upload route to correctly handle multi-part requests
  http_route_set_modifier (upload_route, HTTP_ROUTE_MODIFIER_MULTI_PART)

  # register the new upload route to the HTTP cerver
  http_cerver_route_register (http_cerver, upload_route)

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
  # should be called only once at the start of the program
  cerver_init ()

  # prints the base cerver framework version information
  cerver_version_print_full ()

  # prints the pycerver wrapper version information
  pycerver_version_print_full ()

  # calls the previously defined start () method
  start ()
```
