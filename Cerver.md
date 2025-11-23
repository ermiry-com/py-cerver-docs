# Cerver

`from cerver import ...` has every name of `cerver.core`, `cerver.lib` and `cerver.version`.

| Module | What it has |
|---|---|
| `cerver.core.cerver` | `cerver_init` / `cerver_end`, the creation of a cerver (`cerver_create`, `cerver_create_web`), every setter and getter, `cerver_start`, `cerver_shutdown` and `cerver_teardown` |
| `cerver.core.run` | `cerver_run`, which runs a cerver from the main thread until SIGINT or SIGTERM |
| `cerver.core.policies` | The overload and drain policies, and the drain timeout |
| `cerver.core.update` | The update methods of a cerver, every tick or every interval |
| `cerver.core.handler` | The handlers of the packets of a cerver or a client |
| `cerver.core.events`, `cerver.core.errors` | The events and errors of a cerver, with `cerver_event_on` and `cerver_error_on` |
| `cerver.core.packets`, `cerver.core.connections`, `cerver.core.clients` | The packets, the connections, and the functions with which a cerver handles its clients |
| `cerver.core.files`, `cerver.core.timer`, `cerver.core.auth` | Files and images, time and dates, and the auth errors |

## Life cycle

``` python
cerver_init ()                    # once, at the start of the process

cerver = cerver_create_web (b"web-service", 8080, 16)
cerver_set_thpool_n_threads (cerver, 4)
cerver_set_handler_type (cerver, CERVER_HANDLER_TYPE_THREADS)
cerver_set_reusable_address_flags (cerver, True)

result = cerver_run (cerver)      # until SIGINT or SIGTERM, then cerver_teardown ()

cerver_end ()                     # once, at the end of the process
```

`http_cerver_configuration ("web", port=8080)` of `cerver.http` creates the same web cerver with the configuration of a service in a single call (see [[HTTP]]).

`cerver_run ()` must be called from the main thread, and restores the previous signal handlers before it returns. A cerver that is started some other way is stopped with `cerver_shutdown ()` and released with `cerver_teardown ()`.

## Configuration

The setters take the cerver as their first argument:
- **network:** `cerver_set_network_values`, `cerver_set_bind_address` (it decides the family of the socket), `cerver_set_ipv6_only`, `cerver_set_connection_queue`, `cerver_set_receive_buffer_size`, `cerver_set_reusable_address_flags`, `cerver_set_reusable_port`, `cerver_set_send_timeout`
- **threads:** `cerver_set_thpool_n_threads`, `cerver_set_thpool_queue_size`, `cerver_set_handler_type`, `cerver_set_handle_detachable_threads`
- **policies:** `cerver_set_overload_policy` (`CERVER_OVERLOAD_POLICY_WAIT` or `_REJECT` when the queue of the thpool is full), `cerver_set_drain_policy` and `cerver_set_drain_timeout` (what a shutdown does with the connections that are open)
- **packets:** `cerver_set_app_handlers`, `cerver_set_custom_handler`, `cerver_set_multiple_handlers`, `cerver_set_max_received_packet_size`, `cerver_set_check_packets`, `cerver_set_max_bad_packets`
- **auth and sessions:** `cerver_set_auth`, `cerver_set_auth_method`, `cerver_set_auth_max_tries`, `cerver_set_sessions`, `cerver_set_on_hold_*`
- **UDP:** `cerver_set_udp_peer_timeout` and `cerver_set_udp_max_peers` of [[Network]]

## Events and errors

`cerver_event_on ()` and `cerver_error_on ()` register a plain Python function, and keep it:

``` python
def started (cerver, client, connection):
	cerver_log_success ("The cerver started!")

cerver_event_on (cerver, CERVER_EVENT_STARTED, started)
cerver_event_on (cerver, CERVER_EVENT_CLIENT_CONNECTED, connected, create_thread=True)

def failed (cerver, client, connection, message):
	cerver_log_error (f"Error: {message}")

cerver_error_on (cerver, CERVER_ERROR_PACKET_ERROR, failed)
```

The raw `cerver_event_register ()` and `cerver_error_event_register ()` take a `CerverEventWork` / `CerverErrorWork` that the caller keeps.

## Update methods

``` python
def report (cerver, args):
	cerver_log_msg (f"Handlers working: {cerver_get_n_handlers_working (cerver)}")

# every 60 seconds, each one in a new thread
cerver_update_interval_configure (cerver, report, 60)
```

`cerver_update_configure ()` runs its method every tick instead, with a number of ticks per second.

## Packets and handlers

A cerver of type `CERVER_TYPE_CUSTOM` handles the packets of its clients with a handler:

``` python
def receive (packet_ptr):
	packet = cast (packet_ptr, POINTER (Packet))
	data = packet_data (packet)

handler = handler_create (callback_wrap (receive, HandlerMethod))
handler_set_direct_handle (handler, True)
cerver_set_app_handlers (cerver, handler, None)
```

`packet_send_data (PACKET_TYPE_APP, 0, data, cerver, client, connection)` creates a packet with the data, generates it, sends it and releases it. A packet created with `packet_create ()` has to be generated with `packet_generate ()` before `packet_send ()`.

## Files

- `file_read (path)` and `file_n_read (path, n)` return the bytes of a file (with its NUL bytes), `None` when it can not be read
- `files_get_file_extension`, `files_sanitize` and `files_sanitize_complete` work with `str`
- `files_image_get_type (path)` returns `IMAGE_TYPE_PNG`, `IMAGE_TYPE_JPEG` or `IMAGE_TYPE_NONE` from the bytes of the file
- `file_cerver_add_path` and `file_cerver_set_uploads_path` configure the files of a cerver that sends and receives them in packets
