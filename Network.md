# Network

`from cerver.network import ...` has the addresses, the sockets and the UDP values of cerver.

| Module | What it has |
|---|---|
| `cerver.network.network` | `Protocol`, `SockaddrStorage`, `NetworkAddresses`, the resolution and parsing of addresses, and the `sock_*` options |
| `cerver.network.sockets` | The sockets of cerver: deadlines, writable waits and the sends of a buffer, a vector or a file |
| `cerver.network.udp` | `UdpDatagram`, the states of a peer and the UDP setters of a cerver |

## Addresses

``` python
network_resolve ("localhost", 7000)                   # [("127.0.0.1", 7000), ("::1", 7000)]
network_resolve ("localhost", 7000, prefer_ipv6=True, protocol=PROTOCOL_UDP)

network_parse ("fe80::1%eth0", 80)                    # ("fe80::1%eth0", 80), None if it is not a valid ip
network_hostname_to_ip ("example.com")                # str | None
```

- `network_resolve ()` returns every address of a literal ip or a host name, alternating the families and starting with the preferred one, like a client connection tries them
- `network_parse ()` takes a zone in a link local IPv6 address (`fe80::1%eth0` or `%2`), a zone in any other address is not valid
- `network_address_text (address)` returns the `(ip, port)` of a `SockaddrStorage`, with its zone

The raw `network_resolve_addresses ()`, `network_parse_address ()`, `sock_ip_to_string_actual ()` (with a buffer of `NETWORK_ADDRESS_STRING_SIZE` bytes), `sock_ip_equal ()` (an IPv4 mapped address is equal to its IPv4) and `sock_ip_port ()` work with the structures.

## Protocols and families

`PROTOCOL_TCP` and `PROTOCOL_UDP` are the values of `Protocol`. A cerver binds the family of its bind address (`cerver_set_bind_address ()` of [[Cerver]]): `"::"` with `cerver_set_ipv6_only (cerver, False)` takes IPv4 and IPv6 in a single socket.

## UDP

A cerver created with `PROTOCOL_UDP`, like `cerver_create (CERVER_TYPE_CUSTOM, b"echo", 7000, PROTOCOL_UDP, False, 16)`, receives datagrams of a single packet each, and keeps a peer by address:
- `cerver_set_udp_peer_timeout (cerver, seconds)`: a peer without datagrams for these seconds is removed (`CERVER_UDP_DEFAULT_PEER_TIMEOUT`)
- `cerver_set_udp_max_peers (cerver, peers)`: the most peers at the same time (`CERVER_UDP_DEFAULT_MAX_PEERS`)
- `cerver_udp_peers_count (cerver)`: the peers that it has now

A packet is sent in a single datagram, so it has to fit in `MAX_UDP_PACKET_SIZE` (65507 bytes). The client side is the same `cerver.client`, with a connection of `PROTOCOL_UDP` (see [[Client]]).
