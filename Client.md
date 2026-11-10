# Client

`from cerver.client import ...` has the client of a cerver: it connects to one or more cervers, receives in its own threads, and sends packets and files.

| Module | What it has |
|---|---|
| `cerver.client.client` | The creation of a client, its name, its handlers and its packet limits |
| `cerver.client.connections` | Its connections: connect, start, request, end and teardown |
| `cerver.client.events`, `cerver.client.errors` | The events and errors of a client, with `client_event_on` and `client_error_on` |
| `cerver.client.files` | The files that a client sends and receives |

The connections and packets themselves are in `cerver.core` (see [[Cerver]]), because a cerver and a client share them.

## A client

``` python
def receive (packet_ptr):
	"""The handler of the app packets, it runs in the thread of the connection."""
	answer = packet_data (cast (packet_ptr, POINTER (Packet))).decode ("utf-8")

cerver_init ()

client = client_create ()
client_set_name (client, b"echo-client")

handler = handler_create (callback_wrap (receive, HandlerMethod))
handler_set_direct_handle (handler, True)
client_set_app_handlers (client, handler, None)

client_event_on (client, CLIENT_EVENT_CONNECTED, lambda client, connection: cerver_log_success ("Connected!"))
client_event_on (client, CLIENT_EVENT_DISCONNECTED, lambda client, connection: cerver_log_error ("Disconnected!"))

connection = client_connection_create (client, b"localhost", 7000, PROTOCOL_TCP, False)
connection_set_connect_timeout (connection, 2048)
connection_set_attempt_reconnect (connection, 4)

if (not client_connect_and_start (client, connection)):
	packet_send_data (PACKET_TYPE_APP, 0, b"first", None, client, connection)
	...
	client_connection_end (client, connection)

client_teardown (client)

cerver_end ()
```

- `client_connection_create (client, host, port, protocol, use_ipv6)` creates a connection to a cerver; `use_ipv6` is the family that it tries first
- a connection tries every address that its host resolves to, alternating the families, each one with a non blocking connect of up to `connection_set_connect_timeout ()` milliseconds
- `connection_set_attempt_reconnect (connection, seconds)` reconnects when the cerver ends the connection, resolving the host again
- `client_connect_and_start ()` connects and starts the thread that receives; `client_connect_and_start_async ()` does it in another thread
- the handler of a client gets the packets of type `PACKET_TYPE_APP`; `client_set_custom_handler ()` takes the custom ones
- `client_event_on ()` and `client_error_on ()` take plain Python functions and keep them; the raw `client_event_register ()` and `client_error_register ()` take a `ClientEventWork` / `ClientErrorWork` that the caller keeps

## UDP

A connection of `PROTOCOL_UDP` sends every packet in a single datagram. With a cerver that has authentication, the client sends its auth packet when it connects, the success of the cerver has a datagram token, and every datagram after it carries that token after its packet.

## Files

`client_files_add_path ()` adds a directory whose files a cerver can request, `client_files_set_uploads_path ()` where the received ones are saved, and `client_file_get ()` / `client_file_send ()` request and send a file through a connection.
