# Threads

`from cerver.threads import ...` has the threads, the thread pool, the job queues, the workers and the worker manager of cerver.

| Module | What it has |
|---|---|
| `cerver.threads.threads` | Joinable and detachable threads, their names, mutexes and conditions |
| `cerver.threads.bsem` | Binary semaphores |
| `cerver.threads.thpool` | The thread pool, its queue and its stats |
| `cerver.threads.jobs` | Jobs, job handlers and job queues with priorities, timeouts and stats |
| `cerver.threads.worker` | Workers, and the `Worker` class |
| `cerver.threads.manager` | The worker manager, and the `WorkerManager` class |

The Python functions that cerver calls run in its threads: they take the GIL, so a pure Python work does not run in parallel with another one, but the work of cerver (the I/O, the HTTP parsing) does.

## Thread pool

A web cerver handles its connections in a thpool (`cerver_set_thpool_n_threads ()`). A thpool of its own:

``` python
thpool = thpool_create (4)
thpool_set_queue_size (thpool, 64)
thpool_init (thpool)

thpool_add_work (thpool, work, None)          # a ThpoolWork kept by the caller
thpool_wait (thpool)
thpool_stats (thpool)                          # {"total_jobs": ..., "rejected_jobs": ..., "wait_histogram": [...], ...}

thpool_destroy (thpool)
```

`thpool_add_work_with_reject ()` fails when the queue is full, and `thpool_add_work_wait ()` waits for space. `thpool_stats ()` returns the `ThpoolStats` as a `dict`.

## Job queues

A queue has a ring of jobs for each priority (`JOB_QUEUE_PRIORITY_HIGH`, `_NORMAL`, `_LOW`), and a lower priority job is taken after a number of jobs went ahead of it (`job_queue_set_starvation_limit ()`). `job_queue_pull_timeout ()` and `job_queue_pull_interruptible ()` wait for a job, and `job_queue_stats (queue)` returns its stats as a `dict`.

### A handlers queue

A `JOB_QUEUE_TYPE_HANDLERS` queue runs every job in a single thread, while the requests that push them wait. It keeps a resource that is not thread safe (like a model) in one thread while the cerver has many:

``` python
def predict_work (work: dict) -> None:
	"""Runs in the thread of the queue."""
	work["result"] = model.predict (work["values"])

queue = job_queue_create (JOB_QUEUE_TYPE_HANDLERS)
job_queue_set_handler (queue, callback_wrap (predict_work, JobQueueHandler))
job_queue_start (queue)

# in a handler of a request, it returns when the work was done
work = {"values": values, "result": None}
job_handler_wait (queue, work, None)

# after the service stopped
job_queue_stop (queue)
job_queue_delete (queue)
```

## Worker

A `Worker` runs a Python function with each object that is pushed to it, in its own thread, and keeps every object until it ran:

``` python
worker = Worker ("images", process_image)
worker.start ()

worker.push ({"path": path})

worker.end ()       # waits for the job that is running
worker.delete ()
```

A `Worker` must be released with `delete ()`: it does not call cerver when it is garbage collected.

## Worker manager

A `WorkerManager` adds workers while its jobs wait, and removes the idle ones:

``` python
manager = WorkerManager ("reports", report_build, min_workers=1, max_workers=4)
manager.start ()

manager.push (report, JOB_QUEUE_PRIORITY_HIGH)
manager.stats ()    # the WorkerManagerStats, with the stats of its queue, as a dict

manager.delete ()   # the jobs that are still queued are released with it
```

It also takes `scale_up_wait` (the milliseconds that a job waits without a free worker before one is added) and `idle_timeout` (the milliseconds after which a worker without a job is removed).
