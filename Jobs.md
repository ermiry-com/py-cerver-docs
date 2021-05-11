# HTTP Jobs Example

1. Import the necessary dependencies

``` python
# # provides the ability to handle signals to stop the application
import signal
import sys			# to use sys.exit () to exit the program
import time			# to use time.sleep ()

# needed to interact with the low level cerver framework
import ctypes

# get access to main, HTTP and threads cerver methods
from cerver import *
from cerver.http import *
from cerver.threads import *
```

2. Create a global **Cerver** instance reference

``` python
# define a global reference to the cerver instance
web_cerver = None
```

3. Create a global **JobQueue** instance reference

``` python
# define a global reference to the job queue instance
job_queue = None
```

4. Define a Data object that will be used to share resources between the requests handler threads and the dedicated job queue thread

``` python
class Data ():
	def __init__ (self, value):
		self.value = value		# the input value from the request
		self.result = None		# the result from the operation
```

5. Define an **end** method to be used as the callback when catching signals. We can use this method to display the cerver's stats and to correctly dispose all the memory that has been allocated by our application

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

6. Define the callback method that will be executed by the job queue each time a new data object is pushed

``` python
@ctypes.CFUNCTYPE (None, ctypes.py_object)
def custom_handler_method (data):
	print ("custom_handler_method ()")
	print (data.value.contents.str)
	data.result = "Hello there!"
	time.sleep (1)
```

7. Create a handler methods for the **GET /** route and for the **POST /jobs** route

``` python
# GET /
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def main_handler (http_receive, request):
	# creates and sends a HTTP json message response with the defined status code & message
	http_response_json_msg_send (
		http_receive, HTTP_STATUS_OK, "Main handler!".encode ('utf-8')
	)

# POST /jobs
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def jobs_handler (http_receive, request):
	# tells python to use the global job queue instance reference
	global job_queue

	# get the input value from the request's multi-part values
	value = http_request_multi_parts_get_value (
		request, "value".encode ('utf-8')
	)

	# creates a new data object instance
	data = Data (value)

	# push the data instance to the job queue and wait for the result
	job_handler_wait (job_queue, data, None)

	# at this point the data has been processed in the job queue's dedicated thread using the custom handler and the result can be sent back to the client
	http_response_json_msg_send (
		http_receive, HTTP_STATUS_OK, data.result.encode ('utf-8')
	)
```

8. Define a start method where the **Cerver** instance will be created and its configuration will be set before starting it

``` python
def start ():
	# tell Python to use the global references instead of creating new variables
	global web_cerver
	global job_queue

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

	# GET /
	# create a new HTTP route to handle GET requests in / endpoint using the previously defined main_handler ()
	main_route = http_route_create (
		REQUEST_METHOD_GET,
		"/".encode ('utf-8'), main_handler
	)

	# register the new main route to the HTTP cerver
	http_cerver_route_register (http_cerver, main_route)

	# POST /jobs
	# create a new HTTP route to handle POST requests in /jobs endpoint using the previously defined jobs_handler ()
	jobs_route = http_route_create (
		REQUEST_METHOD_POST, "jobs".encode ('utf-8'), jobs_handler
	)

	# configure the upload route to correctly handle multi-part requests
	http_route_set_modifier (jobs_route, HTTP_ROUTE_MODIFIER_MULTI_PART)

	# register the new jobs route to the HTTP cerver
	http_cerver_route_register (http_cerver, jobs_route)

	# job queue configuration
	# create a new JOB_QUEUE_TYPE_HANDLERS type job queue
	job_queue = job_queue_create (JOB_QUEUE_TYPE_HANDLERS)
	# set the job queue to use the previously defined custom_handler_method () as the callback for each new object that gets pushed
	job_queue_set_handler (job_queue, custom_handler_method)
	# starts the job queue in a dedicated thread
	job_queue_start (job_queue)

	# start listening for connections and handling requests
	cerver_start (web_cerver)
```

9. Define the application's entry point where the **cerver** is initialized and the signals are handled

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
