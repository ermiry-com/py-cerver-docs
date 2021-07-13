# HTTP Multi-Parts

**MultiPartType**

MULTI_PART_TYPE_NONE \
MULTI_PART_TYPE_FILE \
MULTI_PART_TYPE_VALUE

---

**http_multi_part_get_type ()** gets the multi-part's type

``` python
http_multi_part_get_type (
    c_void_p    # reference to a HTTP MultiPart instance
) -> MultiPartType
```

**Returns** the multi-part's type

---

**http_multi_part_is_file ()** checks if the multi-part's type is MULTI_PART_TYPE_FILE

``` python
http_multi_part_is_file (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_bool
```

**Returns** true if the multi-part's type is MULTI_PART_TYPE_FILE

---

**http_multi_part_is_value ()** checks if the multi-part's type is MULTI_PART_TYPE_VALUE

``` python
http_multi_part_is_value (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_bool
```

**Returns** true if the multi-part's type is MULTI_PART_TYPE_VALUE

---

**http_multi_part_get_name ()** gets the multi-part's name

``` python
http_multi_part_get_name (
    c_void_p    # reference to a HTTP MultiPart instance
) -> POINTER (String)
```

**Returns** the multi-part's name

---

**http_multi_part_get_filename ()** gets the multi-part's sanitized original filename

``` python
http_multi_part_get_filename (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_char_p
```

**Returns** the multi-part's sanitized original filename

---

**http_multi_part_get_filename_len ()** gets the multi-part's sanitized original filename length

``` python
http_multi_part_get_filename_len (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_int
```

**Returns** the multi-part's sanitized original filename length

---

**http_multi_part_get_generated_filename ()** gets the multi-part's generated filename

``` python
http_multi_part_get_generated_filename (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_char_p
```

**Returns** the multi-part's generated filename

---

**http_multi_part_get_generated_filename_len ()** gets the multi-part's generated filename length

``` python
http_multi_part_get_generated_filename_len (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_int
```

**Returns** the multi-part's generated filename length

---

**http_multi_part_set_generated_filename ()** sets the HTTP request's multi-part generated filename

``` python
http_multi_part_set_generated_filename (
    c_void_p,   # reference to a HTTP MultiPart instance
    c_char_p    # the format to be used
) -> None
```

---

**http_multi_part_get_n_reads ()** gets the amount of loops it took to read the file

``` python
http_multi_part_get_n_reads (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_uint32
```

**Returns** the multi-part's number of reads value

---

**http_multi_part_get_total_wrote ()** gets the multi-part's total ammount of bytes written to the file

``` python
http_multi_part_get_total_wrote (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_uint32
```

**Returns** the multi-part's total ammount of bytes written to the file

---

**http_multi_part_get_saved_filename ()** gets the multi-part's saved filename

``` python
http_multi_part_get_saved_filename (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_char_p
```

**Returns** the multi-part's saved filename

---

**http_multi_part_get_saved_filename_len ()** gets the multi-part's saved filename length

``` python
http_multi_part_get_saved_filename_len (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_int
```

**Returns** the multi-part's saved filename length

---

**http_multi_part_get_moved_file ()** checks if the file has been moved

``` python
http_multi_part_get_moved_file (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_bool
```

**Returns** true if the file has been moved

---

**http_multi_part_get_value ()** the multi-part's value

``` python
http_multi_part_get_value (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_char_p
```

**Returns** the multi-part's value as a C string reference

---

**http_multi_part_get_value_len ()** gets the multi-part's value length

``` python
http_multi_part_get_value_len (
    c_void_p    # reference to a HTTP MultiPart instance
) -> c_int
```

**Returns** the multi-part's value length

---

**http_multi_part_move_file ()** moves multi-part's saved file to the destination path

``` python
http_multi_part_move_file (
    c_void_p,   # reference to a HTTP MultiPart instance
    c_char_p    # the destination path
) -> c_uint
```

**Returns** 0 on success, 1 on error

---

**http_multi_part_headers_print ()** prints the multi-parts headers

``` python
http_multi_part_headers_print (
    c_void_p    # reference to a HTTP MultiPart instance
) -> None
```

---

**http_multi_part_print ()** prints the multi-parts values

``` python
http_multi_part_print (
    c_void_p    # reference to a HTTP MultiPart instance
) -> None
```
