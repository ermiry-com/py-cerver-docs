# Cerver

``` python
CERVER_DEFAULT_PORT							= 7000
CERVER_DEFAULT_USE_IPV6						= False
CERVER_DEFAULT_CONNECTION_QUEUE				= 10

CERVER_DEFAULT_RECEIVE_BUFFER_SIZE			= 4096

CERVER_DEFAULT_REUSABLE_FLAGS				= False

CERVER_DEFAULT_POOL_THREADS					= 4

CERVER_DEFAULT_SOCKETS_INIT					= 10

CERVER_DEFAULT_POLL_FDS						= 128
CERVER_DEFAULT_POLL_TIMEOUT					= 2000

CERVER_DEFAULT_MAX_INACTIVE_TIME			= 60
CERVER_DEFAULT_CHECK_INACTIVE_INTERVAL		= 30

CERVER_DEFAULT_AUTH_REQUIRED				= False
CERVER_DEFAULT_MAX_AUTH_TRIES				= 2

CERVER_DEFAULT_ON_HOLD_POLL_FDS				= 64
CERVER_DEFAULT_ON_HOLD_TIMEOUT				= 2000
CERVER_DEFAULT_ON_HOLD_MAX_BAD_PACKETS		= 4
CERVER_DEFAULT_ON_HOLD_CHECK_PACKETS		= False
CERVER_DEFAULT_ON_HOLD_RECEIVE_BUFFER_SIZE	= 4096

CERVER_DEFAULT_USE_SESSIONS					= False

CERVER_DEFAULT_MULTIPLE_HANDLERS			= False

CERVER_DEFAULT_CHECK_PACKETS				= False

CERVER_DEFAULT_UPDATE_TICKS					= 30
CERVER_DEFAULT_UPDATE_INTERVAL_SECS			= 1
```

**CerverType**

CERVER_TYPE_NONE \
CERVER_TYPE_CUSTOM \
CERVER_TYPE_GAME \
CERVER_TYPE_WEB \
CERVER_TYPE_FILES

**CerverHandlerType**

CERVER_HANDLER_TYPE_NONE \
CERVER_HANDLER_TYPE_POLL \
CERVER_HANDLER_TYPE_THREADS

## Global

**cerver_init ()** initializes global cerver values. Should be called only once at the start of the program

``` python
cerver_init () -> None
```

**cerver_end ()** correctly disposes global values. Should be called only once at the very end of the program

``` python
cerver_end () -> None
```

## Stats

**cerver_stats_print ()** prints the cerver stats

``` python
cerver_stats_print (
	c_void_p,			# reference to a Cerver instance
	c_bool,				# option to print received stats
	c_bool				# option to print sent stats
) -> None
```

## Main

**cerver_create_web ()** creates a new cerver of type CERVER_TYPE_WEB

``` python
cerver_create_web (
	c_void_p,			# the cerver's name
	c_uint16,			# the cerver's binding port
	c_uint16			# the cerver's connection queue
) -> c_void_p
```

**Returns** a new reference to a Cerver instance

## Configuration

**cerver_set_receive_buffer_size ()** sets the cerver's receive buffer size used in recv method

``` python
cerver_set_receive_buffer_size (
	c_void_p,			# reference to a Cerver instance
	c_size_t			# the size of the buffer in bytes
) -> None
```

---

**cerver_set_thpool_n_threads ()** sets the cerver's thpool number of threads. This will enable the cerver's ability to handle received packets using multiple threads. Useful if you want the best concurrency and effiency. By default, all received packets will be handle only in one thread

``` python
cerver_set_thpool_n_threads (
	c_void_p,			# reference to a Cerver instance
	c_uint16			# the number of threads to be used
) -> None
```

---

**cerver_set_handler_type ()** sets the cerver handler type. The default type is to handle connections using the poll () which requires only one thread. If threads type is selected, a new thread will be created for each new connection

``` python
cerver_set_handler_type (
	c_void_p,			# reference to a Cerver instance
	CerverHandlerType	# the CerverHandlerType value to be used
) -> None
```

---

**cerver_set_reusable_address_flags ()** sets the cerver's ability to use reusable flags in sock fd. This can prevent failing when trying to bind address if TRUE. The default value is CERVER_DEFAULT_REUSABLE_FLAGS

``` python
cerver_set_reusable_address_flags (
	c_void_p,			# reference to a Cerver instance
	c_bool				# the value to enable or disable this option
) -> None
```

**Returns**

## Start

**cerver_start ()** tells the cerver to start listening for connections and packets. Initializes cerver's structures (like thpool) and any other processes that have been configured before

``` python
cerver_start (
	c_void_p			# reference to a Cerver instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

## End

**cerver_shutdown ()** disables socket I/O in both ways and stop any ongoing job

``` python
cerver_shutdown (
	c_void_p			# reference to a Cerver instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error

---

**cerver_teardown ()** tears down a cerver. Stops the cerver and cleans all of its data

``` python
cerver_teardown (
	c_void_p			# reference to a Cerver instance
) -> c_uint8
```

**Returns** 0 on success, 1 on error
