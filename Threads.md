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

**job_new ()** creates a new **Job** instance reference that should be deleted with job_delete ()

``` python
job_new () -> POINTER (Job)
```

**Returns** a new **Job** instance reference

---

**job_delete ()** correctly disposes a **Job** instance reference

``` python
job_delete (
	c_void_p			# a Job instance reference
) -> None
```

---

**job_create ()** creates a new **Job** instance reference with the ability to pass references to the work callback ands its data. It should be deleted with job_delete () 

``` python
job_create (
	JobMethod,			# the jobs's callback method
	c_void_p			# data to be passed to the callback method
) -> POINTER (Job)
```

**Returns** a new **Job** instance reference

---

**job_get ()** gets a **Job** instance reference from the job queue's internal job pool that should be disposed using job_return ()

``` python
job_get (
	c_void_p			# reference to a JobQueue instance
) -> POINTER (Job)
```

**Returns** a **Job** instance reference

---

**job_reset ()** resets the **Job** instance reference internal values

``` python
job_reset (
	c_void_p			# a Job instance reference
) -> None
```

---

**job_return ()** pushes the **Job** instance reference into the job queue's internal job pool

``` python
job_return (
	c_void_p,			# reference to a JobQueue instance
	c_void_p			# a Job instance reference
) -> None
```
