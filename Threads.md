# Threads

## Jobs

**JobMethod** = CFUNCTYPE (None, c_void_p) \
**JobDataDelete** = CFUNCTYPE (None, c_void_p)

``` python
class Job (Structure):
	_fields_ = [
		("method", JobMethod),
		("args", c_void_p),
	]
```

---

**job_new ()**

``` python
job_new () -> POINTER (Job)
```

**Returns**

---

**job_delete ()**

``` python
job_delete (
	c_void_p			# 
) -> None
```

---

**job_create ()**

``` python
job_create (
	JobMethod,			# 
	c_void_p			# 
) -> POINTER (Job)
```

**Returns**

---

**job_get ()**

``` python
job_get (
	c_void_p			# 
) -> POINTER (Job)
```

**Returns**

---

**job_reset ()**

``` python
job_reset (
	c_void_p			# 
) -> None
```

---

**job_return ()**

``` python
job_return (
	c_void_p,			# 
	c_void_p			# 
) -> None
```
