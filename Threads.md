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

## Job Hanlder

**job_handler_new ()** creates a new **JobHandler** instance reference that should be deleted with job_handler_delete ()

``` python
job_handler_new () -> c_void_p
```

**Returns** a new **JobHandler** instance reference

---

**job_handler_delete ()** correctly disposes a **JobHandler** instance reference

``` python
job_handler_delete (
	c_void_p			# reference to a JobHandler instance
) -> None
```

---

**job_handler_create ()** creates a new **JobHandler** instance reference and initializes its internal data. It should be deleted with job_handler_delete ()

``` python
job_handler_create () -> c_void_p
```

**Returns** a new **JobHandler** instance reference

---

**job_handler_get ()** gets a **JobHandler** instance reference from the job queue's handlers pool

``` python
job_handler_get (
	c_void_p			# reference to a JobQueue instance
) -> c_void_p
```

**Returns** a **JobHandler** instance reference

---

**job_handler_reset ()** resets the **JobHandler** instance reference internal values

``` python
job_handler_reset (
	c_void_p			# reference to a JobHandler instance
) -> None
```

---

**job_handler_signal ()** wakes up the waiting thread that called job_handler_wait ()

``` python
job_handler_signal (
	c_void_p			# reference to a JobHandler instance
) -> None
```

---

**job_handler_return ()** pushes the **JobHandler** instance reference into the job queue's internal handlers pool

``` python
job_handler_return (
	c_void_p,			# reference to a JobQueue instance
	c_void_p			# reference to a JobHandler instance
) -> None
```

---

**job_handler_wait ()** pushes a **JobHandler** instance with the data reference and the data delete method to the job queue to be handled by its dedicated thread. The calling thread will wait until job_handler_signal () is called from the handler method

``` python
job_handler_wait (
	c_void_p,			# reference to a JobQueue instance
	py_object,			# reference to an object that will be pased to the job queue's handler method
	JobDataDelete		# a method to dispose the data after it has been used
) -> None
```
