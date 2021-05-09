# HTTP Response

**CatchAllHandler** = CFUNCTYPE (None, c_void_p, c_void_p)
**NotFoundHandler** = CFUNCTYPE (None, c_void_p, c_void_p)
**UploadsFilenameGenerator** = CFUNCTYPE (None, c_void_p, c_char_p, c_char_p)
**UploadsDirnameGenerator** = CFUNCTYPE (POINTER (String), c_void_p)

---

**http_cerver_get ()**

``` python
http_cerver_get (
	c_void_p			#
) -> None
```

**Returns**

## Public

**http_static_path_set_auth ()**

``` python
http_static_path_set_auth (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_static_path_add ()**

``` python
http_cerver_static_path_add (
	c_void_p			#
) -> None
```

**Returns**

---

**http_receive_public_path_remove ()**

``` python
http_receive_public_path_remove (
	c_void_p			#
) -> None
```

**Returns**

## Routes

**http_cerver_set_main_route ()**

``` python
http_cerver_set_main_route (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_route_register ()**

``` python
http_cerver_route_register (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_set_catch_all_route ()**

``` python
http_cerver_set_catch_all_route (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_set_not_found_handler ()**

``` python
http_cerver_set_not_found_handler (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_set_not_found_route ()**

``` python
http_cerver_set_not_found_route (
	c_void_p			#
) -> None
```

**Returns**

## Uploads

**http_cerver_set_uploads_path ()**

``` python
http_cerver_set_uploads_path (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_set_uploads_filename_generator ()**

``` python
http_cerver_set_uploads_filename_generator (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_set_uploads_dirname_generator ()**

``` python
http_cerver_set_uploads_dirname_generator (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_set_uploads_delete_when_done ()**

``` python
http_cerver_set_uploads_delete_when_done (
	c_void_p			#
) -> None
```

**Returns**

## Auth

**http_jwt_get_bearer ()**

``` python
http_jwt_get_bearer (
	c_void_p			#
) -> None
```

**Returns**

---

**http_jwt_get_bearer_len ()**

``` python
http_jwt_get_bearer_len (
	c_void_p			#
) -> None
```

**Returns**

---

**http_jwt_get_json ()**

``` python
http_jwt_get_json (
	c_void_p			#
) -> None
```

**Returns**

---

**http_jwt_get_json_len ()**

``` python
http_jwt_get_json_len (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_set_jwt_algorithm ()**

``` python
http_cerver_auth_set_jwt_algorithm (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_set_jwt_priv_key_filename ()**

``` python
http_cerver_auth_set_jwt_priv_key_filename (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_set_jwt_pub_key_filename ()**

``` python
http_cerver_auth_set_jwt_pub_key_filename (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_jwt_new ()**

``` python
http_cerver_auth_jwt_new (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_jwt_delete ()**

``` python
http_cerver_auth_jwt_delete (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_jwt_add_value ()**

``` python
http_cerver_auth_jwt_add_value (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_jwt_add_value_bool ()**

``` python
http_cerver_auth_jwt_add_value_bool (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_jwt_add_value_int ()**

``` python
http_cerver_auth_jwt_add_value_int (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_auth_generate_bearer_jwt_json ()**

``` python
http_cerver_auth_generate_bearer_jwt_json (
	c_void_p			#
) -> None
```

**Returns**

## Stats

**http_cerver_all_stats_print ()**

``` python
http_cerver_all_stats_print (
	c_void_p			#
) -> None
```

**Returns**

## Admin

**http_cerver_enable_admin_routes ()**

``` python
http_cerver_enable_admin_routes (
	c_void_p			#
) -> None
```

**Returns**

---

**http_cerver_register_admin_file_system ()**

``` python
http_cerver_register_admin_file_system (
	c_void_p			#
) -> None
```

**Returns**

## Handler

**http_receive_get_cerver ()**

``` python
http_receive_get_cerver (
	c_void_p			#
) -> None
```

**Returns**
