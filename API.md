# HTTP API Example

0. Create both private & public keys that will be used to sign and decode the generated JWT to handle authentication in private routes

```
mkdir keys
ssh-keygen -t rsa -b 4096 -m PEM -f ./keys/key.key
openssl rsa -in ./keys/key.key -pubout -outform PEM -out ./keys/key.pub
```

1. Define custom **User** class and methods

``` python
# store users in memory
users = []

# custom User class
class User ():
  def __init__ (self, id, iat, name, role, username):
    self.id = id
    self.iat = iat
    self.name = name
    self.role = role
    self.username = username

    self.password = None

  def __str__ (self):
    return f'User: \n\t{self.id} \n\t{self.name} \n\t{self.username} \n\t{self.role}'

# adds a newly created user to the user's list
def user_add (user):
  users.append (user)

# search an existing user by username in the user's list
def user_get_by_username (username):
  for u in users:
    if u.username == username:
      return u

  return None

```

2. Import the necessary dependencies

``` python
# provides the ability to handle signals to stop the application
import signal
import sys    # to use sys.exit () to exit the program

import time
import random
import json

# needed to interact with the low level cerver framework
import ctypes

# get access to main and HTTP cerver methods
from cerver import *
from cerver.http import *
```

3. Create a global **Cerver** instance reference

``` python
# define a global reference to the cerver instance
web_service = None
```

4. Define an **end** method to be used as the callback when catching signals. We can use this method to display the cerver's stats and to correctly dispose all the memory that has been allocated by our application

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

5. Create handler methods for each api route

``` python
# GET /api/users
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def main_users_handler (http_receive, request):
  response = http_response_json_msg (
    HTTP_STATUS_OK, b"Users route works!"
  )

  http_response_print (response)
  http_response_send (response, http_receive)
  http_response_delete (response)

# POST /api/users/register
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def users_register_handler (http_receive, request):
  body_values = http_request_get_body_values (request)
  name = http_query_pairs_get_value (body_values, b"name");
  username = http_query_pairs_get_value (body_values, b"username");
  password = http_query_pairs_get_value (body_values, b"password");

  if (name is not None and username is not None and password is not None):
    user = User (
      str (random.randint (1, 1001)),
      int (time.time ()),
      name.contents.str,
      "common",
      username.contents.str
    )

    user.password = password.contents.str

    user_add (user)

    response = http_response_json_msg (
      HTTP_STATUS_OK, b"Created a new user!"
    )

    http_response_print (response)
    http_response_send (response, http_receive)
    http_response_delete (response)

  else:
    response = http_response_json_msg (
      HTTP_STATUS_BAD_REQUEST, b"Missing user values!"
    )

    http_response_print (response)
    http_response_send (response, http_receive)
    http_response_delete (response)

# POST /api/users/login
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def users_login_handler (http_receive, request):
  body_values = http_request_get_body_values (request)
  username = http_query_pairs_get_value (body_values, b"username");
  password = http_query_pairs_get_value (body_values, b"password");

  if (username is not None and password is not None):
    user = user_get_by_username (username.contents.str)
    if (user is not None):
      if (user.password == password.contents.str):
        http_jwt = http_cerver_auth_jwt_new ()
        http_cerver_auth_jwt_add_value_int (
          http_jwt, b"iat", int (time.time ())
        )
        http_cerver_auth_jwt_add_value (
          http_jwt, b"id", user.id.encode ("utf-8")
        )
        http_cerver_auth_jwt_add_value (
          http_jwt, b"name", user.name
        )
        http_cerver_auth_jwt_add_value (
          http_jwt, b"username", user.username
        )
        http_cerver_auth_jwt_add_value (
          http_jwt, b"role", user.role.encode ("utf-8")
        )

        http_cerver_auth_generate_bearer_jwt_json (
          http_receive_get_cerver (http_receive), http_jwt
        )

        response = http_response_create (
          HTTP_STATUS_OK, http_jwt_get_json (http_jwt),
          http_jwt_get_json_len (http_jwt)
        )

        http_cerver_auth_jwt_delete (http_jwt)

        http_response_compile (response);
        http_response_print (response)
        http_response_send (response, http_receive)
        http_response_delete (response)
      else:
        response = http_response_json_msg (
          HTTP_STATUS_BAD_REQUEST, b"Wrong password!"
        )

        http_response_print (response)
        http_response_send (response, http_receive)
        http_response_delete (response)
    else:
      response = http_response_json_msg (
        HTTP_STATUS_NOT_FOUND, b"User not found!"
      )

      http_response_print (response)
      http_response_send (response, http_receive)
      http_response_delete (response)
  else:
    response = http_response_json_msg (
      HTTP_STATUS_BAD_REQUEST, b"Missing user values!"
    )

    http_response_print (response)
    http_response_send (response, http_receive)
    http_response_delete (response)

# GET /api/users/profile
@ctypes.CFUNCTYPE (None, ctypes.c_void_p, ctypes.c_void_p)
def users_profile_handler (http_receive, request):
  json_string = ctypes.cast (http_request_get_decoded_data (request), ctypes.c_char_p)
  user_dict = json.loads (json_string.value)
  user = User (**user_dict)

  message = "%s profile!" % (user.username)

  response = http_response_json_msg (
    HTTP_STATUS_OK, message.encode ("utf-8")
  )

  http_response_print (response)
  http_response_send (response, http_receive)
  http_response_delete (response)
```

6. Define a start method where the **Cerver** instance will be created and its configuration will be set before starting it

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

  # configure the cerver to handle authentication using JWT with RS256
  http_cerver_auth_set_jwt_algorithm (http_cerver, JWT_ALG_RS256)

  # configure the path for the private key
  http_cerver_auth_set_jwt_priv_key_filename (http_cerver, b"keys/key.key")

  # configure the path for the public key
  http_cerver_auth_set_jwt_pub_key_filename (http_cerver, b"keys/key.pub")

  # GET /api/users
  # create the HTTP top-level route to handle GET requests in /api/users endpoint using the previously defined main_users_handler ()
  users_route = http_route_create (
    REQUEST_METHOD_GET, b"api/users", main_users_handler
  )

  # register the new top-level route to the HTTP cerver
  http_cerver_route_register (http_cerver, users_route)

  # POST api/users/register
  # create a new route to handle POST requests in api/users/register endpoint
  users_register_route = http_route_create (
    REQUEST_METHOD_POST, b"register", users_register_handler
  )

  # register the register route as a child of the top level route
  http_route_child_add (users_route, users_register_route)

  # POST api/users/login
  # create a new route to handle POST requests in api/users/login endpoint
  users_login_route = http_route_create (
    REQUEST_METHOD_POST, b"login", users_login_handler
  )

  # register the login route as a child of the top level route
  http_route_child_add (users_route, users_login_route)

  # GET api/users/profile
  # create a new route to handle GET requests in api/users/profile endpoint
  users_profile_route = http_route_create (
    REQUEST_METHOD_GET, b"profile", users_profile_handler
  )

  # enable authentication using bearer JWT in the profile route
  http_route_set_auth (users_profile_route, HTTP_ROUTE_AUTH_TYPE_BEARER)
  # configure to decode data into a json object on success auth
  http_route_set_decode_data_into_json (users_profile_route)

  # register the profile route as a child of the top level route
  http_route_child_add (users_route, users_profile_route)

  # start listening for connections and handling requests
  cerver_start (web_service)
```

7. Define the application's entry point where the **cerver** is initialized and the signals are handled

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
