# HTTP Headers

|	Name								|	String								|	Description																									|
|---------------------------------------|---------------------------------------|---------------------------------------------------------------------------------------------------------------|
|	AUTHENTICATE	 					|	WWW-Authenticate					|	Defines the authentication method that should be used to access a resource.									|
|	AUTHORIZATION	 					|   Authorization						|	Contains the credentials to authenticate a user-agent with a server.										|
|	PROXY_AUTHENTICATE					|	Proxy-Authenticate					|	Defines the authentication method that should be used to access a resource behind a proxy server.			|
|	PROXY_AUTHORIZATION					|	Proxy-Authorization					|	Contains the credentials to authenticate a user agent with a proxy server.									|
|	AGE									|	Age									|	The time (in seconds) that the object has been in a proxy cache.											|
|	CACHE_CONTROL						|	Cache-Control						|	Directives for caching mechanisms in both requests and responses.											|
|	CLEAR_SITE_DATA						|	Clear-Site-Data						|	Clears browsing data (e.g. cookies, storage, cache) associated with the requesting website.					|
|	EXPIRES	 							|	Expires								|	The date/time after which the response is considered stale.													|
|	WARNING								|	Warning								|	General warning information about possible problems.														|
|	CONNECTION							|	Connection							|	Controls whether the network connection stays open after the current transaction finishes.					|
|	KEEP_ALIVE							|	Keep-Alive							|	Controls how long a persistent connection should stay open.													|
|	ACCEPT								|	Accept								|	Informs the server about the types of data that can be sent back.											|
|	ACCEPT_CHARSET	 					|	Accept-Charset						|	Which character encodings the client understands.															|
|	ACCEPT_ENCODING	 					|	Accept-Encoding						|	The encoding algorithm, usually a compression algorithm, that can be used on the resource sent back.		|
|	ACCEPT_LANGUAGE	 					|	Accept-Language						|	Informs the server about the human language the server is expected to send back.							|
|	EXPECT	 							|	Expect								|	Indicates expectations that need to be fulfilled by the server to properly handle the request.				|
|	COOKIE	 							|	Cookie								|	Contains stored HTTP cookies previously sent by the server with the Set-Cookie header.						|
|	SET_COOKIE	 						|	Set-Cookie							|	Send cookies from the server to the user-agent.																|
|	ACCESS_CONTROL_ALLOW_ORIGIN	 		|	Access-Control-Allow-Origin			|	Indicates whether the response can be shared.																|
|	ACCESS_CONTROL_ALLOW_CREDENTIALS	|	Access-Control-Allow-Credentials	|	Indicates whether the response to the request can be exposed when the credentials flag is true.				|
|	ACCESS_CONTROL_ALLOW_HEADERS	 	|	Access-Control-Allow-Headers		|	Used in response to a preflight request to indicate which headers can be used when making the request.		|
|	ACCESS_CONTROL_ALLOW_METHODS	 	|	Access-Control-Allow-Methods		|	Specifies the methods allowed when accessing the resource in response to a preflight request.				|
|	ACCESS_CONTROL_EXPOSE_HEADER	 	|	Access-Control-Expose-Headers		|	Indicates which headers can be exposed as part of the response by listing their names.						|
|	ACCESS_CONTROL_MAX_AGE				|	Access-Control-Max-Age				|	Indicates how long the results of a preflight request can be cached.										|
|	ACCESS_CONTROL_REQUEST_HEADERS	 	|	Access-Control-Request-Headers		|	Used let the server know which HTTP headers will be used when the actual request is made.					|
|	ACCESS_CONTROL_REQUEST_METHOD	 	|	Access-Control-Request-Method		|	Used to let the server know which method will be used when the actual request is made.						|
|	ORIGIN								|	Origin 								|	Indicates where a fetch originates from.																	|
|	TIMING_ALLOW_ORIGIN					|	Timing-Allow-Origin					|	Specifies origins that are allowed to see values retrieved via the Resource Timing API.						|
|	CONTENT_DISPOSITION					|	Content-Disposition					|	Indicates if the resource is a download and the browser should present a “Save As” dialog.					|
|	CONTENT_LENGTH						|	Content-Length						|	The size of the resource, in decimal number of bytes.														|
|	CONTENT_TYPE						|	Content-Type						|	Indicates the media type of the resource.																	|
|	CONTENT_ENCODING					|	Content-Encoding					|	Used to specify the compression algorithm.																	|
|	CONTENT_LANGUAGE					|	Content-Language					|	Describes the human language(s) intended for the audience.													|
|	CONTENT_LOCATION					|	Content-Location					|	Indicates an alternate location for the returned data.														|
|	FORWARDED							|	Forwarded							|	Contains info from the side of proxies that is altered when a proxy is in the path of the request.			|
|	VIA	 								|	Via									|	Added by proxies, both forward and reverse proxies, and can appear in the request and the response.			|
|	LOCATION	 						|	Location							|	Indicates the URL to redirect a page to.																	|
|	FROM	 							|	From								|	Contains an Internet email address for a human user who controls the requesting user agent.					|
|	HOST	 							|	Host								|	Specifies the domain name of the server, and (optionally) the TCP port on which the server is listening.	|
|	REFERER	 							|	Referer								|	The address of the previous web page from which a link to the currently requested page was followed.		|
|	REFERRER_POLICY	 					|	Referrer-Policy						|	Governs which referrer information sent in the Referer header should be included with requests made.		|
|	USER_AGENT							|	User-Agent							|	Contains a characteristic string that allows the network protocol peers to identify the application type, operating system, software vendor or software version of the requesting software user agent.																									  |
|	ALLOW	 							|	Allow								|	Lists the set of HTTP request methods supported by a resource.												|
|	SERVER	 							|	Server								|	Contains information about the software used by the origin server to handle the request.					|
|	ACCEPT_RANGES						|	Accept-Ranges						|	Indicates if the server supports range requests, and if so in which unit the range can be expressed.		|
|	RANGE	 							|	Range								|	Indicates the part of a document that the server should return.												|
|	IF_RANGE							|	If-Range							|	Creates a conditional range request that is only fulfilled if the given tag or date matches the remote resource.Used to prevent downloading two ranges from incompatible version of the resource.																												|
|	CONTENT_RANGE	 					|	Content-Range						|	Indicates where in a full body message a partial message belongs.											|
|	SEC_WEBSOCKET_KEY					|	Sec-WebSocket-Key					|	No description																								|
|	SEC_WEBSOCKET_EXTENSIONS			|	Sec-WebSocket-Extensions			|	No description																								|
|	SEC_WEBSOCKET_ACCEPT				|	Sec-WebSocket-Accept				|	No description																								|
|	SEC_WEBSOCKET_PROTOCOL				|	Sec-WebSocket-Protocol				|	No description																								|
|	SEC_WEBSOCKET_VERSION				|	Sec-WebSocket-Version				|	No description																								|
|	DATE								|	Date								|	Contains the date and time at which the message was originated.												|
|	UPGRADE								|	Upgrade								|	The standard establishes rules for upgrading or changing to a different protocol on the current client, server, transport protocol connection.			|
|	INVALID	 							|	Undefined							|	Invalid Header																								|

---

**http_header_string ()** matches the **HttpHeader** value with its string representation

``` python
http_header_string (
	HttpHeader			# the HttpHeader value to be matched
) -> c_char_p
```

**Returns** a constant string reference to the matching **HttpHeader** string representation

---

**http_header_description ()** matches the **HttpHeader** value with its description

``` python
http_header_description (
	HttpHeader			# the HttpHeader value to be matched
) -> c_char_p
```

**Returns** a constant string reference to the matching **HttpHeader** description

---

**http_header_type_by_string ()** gets the correct **HttpHeader** value for the matching header string

``` python
http_header_type_by_string (
	c_char_p			# the string reference to be matched
) -> HttpHeader
```

**Returns** the correct **HttpHeader** value for the matching header string
