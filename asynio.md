```
What I thought - what is actually true?
- "if we want the code to not keep waiting when some long running step is not using cpu but is sending or receiving data from some input or output device, or sending or receiving data from a network request and the at least one core of cpu is idle, then this concept of asyncio comes into picture."
  Verdict: Not correct. asyncio is for single‑threaded concurrency built on non‑blocking I/O multiplexing, allowing one thread to interleave many I/O-bound operations; it is not primarily about keeping multiple CPU cores busy. Correct statement: Use asyncio to handle many I/O‑bound tasks concurrently on a single thread via an event loop and readiness-based I/O, not to utilize otherwise idle CPU cores.[4][3]

- "to efficiently use cpu and not keep its cores idle, what we do is write async before function def that makes the function become a coroutine."
  Verdict: Not correct. The purpose of async/await is cooperative concurrency for I/O; CPU‑core utilization is not the goal, and async def simply defines a coroutine function that can be awaited. Correct statement: async def declares a coroutine function, and concurrency arises when it hits await on a pending awaitable; it does not make code use additional CPU cores.[3][1]

- "and inside that function that has a IO or network step we write await before it."
  Verdict: Correct with nuance. You await an awaitable (e.g., coroutine, Task, or Future) representing an I/O operation so the coroutine can suspend without blocking the thread. Clarification: await is valid only on awaitables; doing so yields control to the event loop while the operation is pending, enabling other tasks to run.[5][1]

- "now we also ensure the entire code is being handled by a Eventloop."
  Verdict: Not correct. Only asynchronous parts run under an event loop; ordinary synchronous code runs as usual and can interoperate with async via entry points like asyncio.run or by scheduling tasks. Correct statement: The event loop drives async tasks/callbacks and manages I/O readiness; you enter it explicitly (e.g., asyncio.run) and schedule coroutines/Tasks to run within it.[2][3]

- "Event loop is a scheduler that uses 1 thread and schedules coroutines on that thread; Python process handles this event loop."
  Verdict: Not fully correct. The loop runs in a single thread and schedules tasks cooperatively, but it is per thread (one running loop per thread) rather than a special, process‑wide entity. Correct statement: An event loop runs in a single thread and schedules coroutines/Tasks there; a typical program uses one main loop, and asyncio.run ensures a fresh loop for the call.[2][3]

- "coroutine that is waiting for some IO or network input/output is taken out of the thread and another coroutine is put on that thread."
  Verdict: Not correct. Nothing is moved between threads; the coroutine suspends at await and the loop switches to another ready task in the same thread. Correct statement: When a coroutine awaits a pending awaitable, it yields control; the event loop later resumes it and runs other ready tasks in the interim on the same thread.[1][3]

- "once a coroutine IO or network task is done, OS will notify eventloop and then it tries to put that coroutine back on the thread it is managing when possible"
  Verdict: Mostly correct but imprecise. The event loop uses I/O multiplexing (selectors like epoll/kqueue) to detect readiness and schedules the waiting task to resume; nothing is “put back on the thread” because everything already runs on that one thread. Correct statement: The loop monitors file descriptor readiness via the OS selector and, upon readiness, schedules the awaiting task to continue execution on the same loop thread.[4][3]

- "whatever coroutines are on that thread will be executed on one cpu core."
  Verdict: Correct with nuance. A single event‑loop thread runs on one core at a time; CPU‑bound parallelism requires threads or processes (e.g., run_in_executor or multiprocessing). Clarification: asyncio provides concurrency for I/O; for true CPU‑parallel work, offload to worker threads/processes to avoid blocking the loop.[5][3]

- "now when coroutine is running on that thread its statements are running in sequence."
  Verdict: Correct. A coroutine executes sequentially until it reaches an await that suspends it; suspension points are explicit. Clarification: Without hitting an await that’s pending, the coroutine continues synchronously like a regular function call.[1]

- "then when it reaches the await statement in it , it understands that it will take time to complete that line , so gives control to event looop and event loop takes it off the thread..and puts another one.."
  Verdict: Not fully correct. await suspends only if the awaited operation is not ready; the loop then runs other ready tasks, but nothing is moved off or between threads. Correct statement: At await on a pending awaitable, the coroutine yields to the loop, which resumes it when ready and meanwhile schedules other tasks on the same thread.[5][1]

- "asyncio.run (main()) is written to ensuree an event loop is started so that any async def functions are treated as coroutines and handled by it."
  Verdict: Mostly correct. asyncio.run creates a fresh event loop, runs the given coroutine to completion, finalizes async generators, and then closes the loop; it cannot be called if a loop is already running in that thread. Correct statement: Use asyncio.run(main()) as the main entry point; it always creates and closes a new loop and refuses to run inside an already running loop.[2]

- "if we declare function as async but dont write any await then there is no use of writing async because you are saying it is a coroutine ..like a cooperating routine but you are not saying on which line it is going to cooperate by giving away control to event loop and let other coroutines be run on that thread that the python's eventlooop is managing."
  Verdict: Partly correct. An async function without awaits still returns a coroutine, but when awaited it will run to completion without yielding, so it doesn’t provide concurrency benefits. Correct statement: Such functions are valid (often for API uniformity) but have no suspension points and thus don’t interleave with other tasks; forgetting to await them produces a “coroutine was never awaited” warning.[1][5]

[1](https://docs.python.org/3/library/asyncio-task.html)
[2](https://stackoverflow.com/questions/55590343/asyncio-run-or-run-until-complete)
[3](https://docs.python.org/es/3.6/library/asyncio.html)
[4](https://tenthousandmeters.com/blog/python-behind-the-scenes-12-how-asyncawait-works-in-python/)
[5](https://realpython.com/async-io-python/)
[6](https://docs.python.org/zh-cn/3.6/library/asyncio-task.html)
[7](https://fastapi.tiangolo.com/async/)
[8](https://bbc.github.io/cloudfit-public-docs/asyncio/asyncio-part-2.html)
[9](https://www.cmi.ac.in/~madhavan/courses/prog2-2015/docs/python-3.4.2-docs-html/library/asyncio-task.html)
[10](https://stackoverflow.com/questions/31623194/asyncio-two-loops-for-different-i-o-tasks)
[11](https://docs.telethon.dev/en/stable/concepts/asyncio.html)
[12](https://stackoverflow.com/questions/40571786/asyncio-coroutine-vs-async-def)
[13](https://docs.micropython.org/en/latest/library/asyncio.html)
[14](https://docs.scrapy.org/en/latest/topics/coroutines.html)
[15](https://www.cmi.ac.in/~madhavan/courses/prog2-2015/docs/python-3.4.2-docs-html/library/asyncio.html)
[16](https://github.com/run-llama/llama_index/issues/9978)
[17](https://developers.llamaindex.ai/python/framework/getting_started/async_python/)
[18](https://leapcell.io/blog/mastering-python-async-io-fastapi)
[19](https://us-pycon-2019-tutorial.readthedocs.io/asyncio_intro.html)
[20](https://pydle.readthedocs.io/en/v0.9.0/_modules/asyncio/coroutines.html)

```