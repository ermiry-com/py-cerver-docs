# HTTP Admin Example

1. Import the necessary dependencies

``` python
# # provides the ability to handle signals to stop the application
import signal
import sys			# to use sys.exit () to exit the program

# needed to interact with the low level cerver framework
import ctypes

# get access to main and HTTP cerver methods
from cerver import *
from cerver.http import *
```

2. Create a global **Cerver** instance reference

``` python
# define a global reference to the cerver instance
web_cerver = None
```

3. Define an **end** method to be used as the callback when catching signals. We can use this method to display the cerver's stats and to correctly dispose all the memory that has been allocated by our application

``` python
def end (signum, frame):
	# prints the HTTP cerver stats like how many requests were handled by each route
	http_cerver_all_stats_print (http_cerver_get (web_cerver))

	# correctly disposes HTTP cerver references
	cerver_teardown (web_cerver)

	# correctly disposes cerver internal global values. Should be called only once at the very end of the program
	cerver_end ()

	# exit the application with a custom message
	sys.exit ("Done!")
```

4. Create a handler method for the **GET /** route

``` python
# GET /
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def main_handler (http_receive, request):
	# creates a HTTP response with the defined status code and a data (body) with a json message of type { msg: "your message" } ready to be sent
	response = http_response_json_msg (
		HTTP_STATUS_OK, "Admin HTTP cerver!".encode ('utf-8')
	)

	# print the generated HTTP response
	http_response_print (response)

	# send the HTTP response to the client
	http_response_send (response, http_receive)

	# correctly disposes a HTTP response and all of its data
	http_response_delete (response)
```

5. Define a start method where the **Cerver** instance will be created and its configuration will be set before starting it

``` python
def start ():
	# tell Python to use the global reference instead of creating a new variable
	global web_cerver

	# create a new cerver of type CERVER_TYPE_WEB that will bind and listen to port 8080 and with a connection queue of size 10
	web_cerver = cerver_create_web (
		"web-cerver".encode ('utf-8'), 8080, 10
	)

	# main configuration
	# set the cerver's thpool number of threads. This will enable the cerver's ability to handle requests using multiple threads
	cerver_set_thpool_n_threads (web_cerver, 4)
	cerver_set_handler_type (web_cerver, CERVER_HANDLER_TYPE_THREADS)

	# enable the cerver's reusable address flags to avoid errors when binding to the selected port
	cerver_set_reusable_address_flags (web_cerver, True)

	# HTTP configuration
	# get a reference to the cerver's internal HTTP structure
	http_cerver = http_cerver_get (web_cerver)

	# enable admin routes in the HTTP cerver
	http_cerver_enable_admin_routes (http_cerver, True)

	# GET /
	# create a new HTTP route to handle GET requests in / endpoint using the previously defined main_handler ()
	main_route = http_route_create (
		REQUEST_METHOD_GET,
		"/".encode ('utf-8'), main_handler
	)

	# register the new main route to the HTTP cerver
	http_cerver_route_register (http_cerver, main_route)

	# start listening for connections and handling requests
	cerver_start (web_cerver)
```

6. Define the application's entry point where the **cerver** is initialized and the signals are handled

``` python
if __name__ == "__main__":
	# register to the desired signals and sets the end () method to handle them
	signal.signal (signal.SIGINT, end)
	signal.signal (signal.SIGTERM, end)
	signal.signal (signal.SIGPIPE, signal.SIG_IGN)

	# initializes global cerver values. Should be called only once at the start of the program
	cerver_init ()

	# prints the base cerver framework version information
	cerver_version_print_full ()

	# prints the pycerver wrapper version information
	pycerver_version_print_full ()

	# calls the previously defined start () method
	start ()
```
