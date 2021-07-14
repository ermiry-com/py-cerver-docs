# HTTP Origin

**http_origin_new ()** allocates a new HttpOrigin instance

``` python
http_origin_new (None) -> POINTER (HttpOrigin)
```

**Returns** a newly allocated HttpOrigin instance

---

**http_origin_delete ()** deletes a HTTP Origin instance

``` python
http_origin_delete (
    c_void_p    # reference to a HTTP Origin instance
) -> None
```

---

**http_origin_get_len ()** gets the HTTP origin value length

``` python
http_origin_get_len (
    c_void_p    # reference to a HTTP Origin instance
) -> c_int
```

**Returns** the HTTP origin value length

---

**http_origin_get_value ()** gets the HTTP origin value

``` python
http_origin_get_value (
    c_void_p    # reference to a HTTP origin instance
) -> c_char_p
```

**Returns** the HTTP origin value as a C string reference

---

**http_origin_print ()**

``` python
http_origin_print (
    c_void_p    # reference to a HTTP origin instance
) -> None
```
