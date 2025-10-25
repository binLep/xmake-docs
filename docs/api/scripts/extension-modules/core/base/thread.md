# core.base.thread

Provides native thread support for concurrent programming, including thread creation, synchronization primitives, and inter-thread communication.

## thread.start

- Start a thread

#### Function Prototype

::: tip API
```lua
thread.start(callback: <function>, ...)
```
:::

#### Parameter Description

| Parameter | Description |
|-----------|-------------|
| callback | Required. Callback function to execute in the thread |
| ... | Optional. Additional arguments passed to the callback function |

#### Usage

Creates and starts a thread to execute a callback function.

Return Value: Returns a thread object that can be used to wait for thread completion

::: tip NOTE
Each thread is a separate Lua VM instance, their Lua variable states are completely isolated and cannot be directly shared. Parameters are passed unidirectionally and internally serialized, therefore only support serializable parameters like `string`, `table`, `number`, etc.
:::

## thread.start_named

- Start a named thread

#### Function Prototype

::: tip API
```lua
thread.start_named(name: <string>, callback: <function>, ...)
```
:::

#### Parameter Description

| Parameter | Description |
|-----------|-------------|
| name | Required. Thread name |
| callback | Required. Callback function to execute in the thread |
| ... | Optional. Additional arguments passed to the callback function |

#### Usage

Creates and starts a new thread with the specified name and callback function.

Return Value: Returns a thread object that can be used to wait for thread completion

::: tip NOTE
Parameters are passed unidirectionally and internally serialized, therefore only support serializable parameters like `string`, `table`, `number`, etc.
:::

### Example

```lua
import("core.base.thread")

function callback(id)
    import("core.base.thread")
    print("%s: %d starting ..", thread.running(), id)
    for i = 1, 10 do
        print("%s: %d", thread.running(), i)
        os.sleep(1000)
    end
    print("%s: %d end", thread.running(), id)
end

function main()
    local t0 = thread.start_named("thread_0", callback, 0)
    local t1 = thread.start_named("thread_1", callback, 1)
    t0:wait(-1)
    t1:wait(-1)
end
```

## thread.running

- Get the current thread name

#### Function Prototype

::: tip API
```lua
thread.running()
```
:::

#### Parameter Description

No parameters required for this function.

#### Usage

Returns the name of the currently running thread.

Returns the name of the current thread as a string.

## thread.mutex

- Create a mutex object

#### Function Prototype

::: tip API
```lua
thread.mutex()
```
:::

#### Parameter Description

No parameters required for this function.

#### Usage

Creates a new mutex for thread synchronization.

Return Value: Returns a mutex object with the following methods: `mutex:lock()` lock the mutex, `mutex:unlock()` unlock the mutex

::: tip NOTE
Mutex can be accessed across threads for inter-thread synchronization.
:::

### Example

```lua
import("core.base.thread")

function callback(mutex)
    import("core.base.thread")
    print("%s: starting ..", thread.running())
    for i = 1, 10 do
        mutex:lock()
        print("%s: %d", thread.running(), i)
        mutex:unlock()
        os.sleep(1000)
    end
    print("%s: end", thread.running())
end

function main()
    local mutex = thread.mutex()
    local t0 = thread.start_named("thread_0", callback, mutex)
    local t1 = thread.start_named("thread_1", callback, mutex)
    t0:wait(-1)
    t1:wait(-1)
end
```

## thread.event

- Create an event object

#### Function Prototype

::: tip API
```lua
thread.event()
```
:::

#### Parameter Description

No parameters required for this function.

#### Usage

Creates a new event for thread signaling and synchronization.

Return Value: Returns an event object with the following methods: `event:wait(timeout)` wait for the event to be signaled, `event:post()` signal the event

::: tip NOTE
Event object can be accessed across threads for inter-thread signaling.
:::

### Example

```lua
import("core.base.thread")

function callback(event)
    import("core.base.thread")
    print("%s: starting ..", thread.running())
    while true do
        print("%s: waiting ..", thread.running())
        if event:wait(-1) > 0 then
            print("%s: triggered", thread.running())
        end
    end
end

function main()
    local event = thread.event()
    local t = thread.start_named("keyboard", callback, event)
    while true do
        local ch = io.read()
        if ch then
            event:post()
        end
    end
    t:wait(-1)
end
```

## thread.semaphore

- Create a semaphore object

#### Function Prototype

::: tip API
```lua
thread.semaphore(name: <string>, initial_count: <number>)
```
:::

#### Parameter Description

| Parameter | Description |
|-----------|-------------|
| name | Required. Semaphore name |
| initial_count | Required. Initial count value |

#### Usage

Creates a new semaphore for thread synchronization and resource counting.

Return Value: Returns a semaphore object with the following methods: `semaphore:wait(timeout)` wait for semaphore (decrement count), `semaphore:post(count)` post to semaphore (increment count)

::: tip NOTE
Semaphore can be accessed across threads for inter-thread resource counting and synchronization.
:::

### Example

```lua
import("core.base.thread")

function callback(semaphore)
    import("core.base.thread")
    print("%s: starting ..", thread.running())
    while true do
        print("%s: waiting ..", thread.running())
        if semaphore:wait(-1) > 0 then
            print("%s: triggered", thread.running())
        end
    end
end

function main()
    local semaphore = thread.semaphore("", 1)
    local t = thread.start_named("keyboard", callback, semaphore)
    while true do
        local ch = io.read()
        if ch then
            semaphore:post(2)
        end
    end
    t:wait(-1)
end
```

## thread.queue

- Create a thread-safe queue object

#### Function Prototype

::: tip API
```lua
thread.queue()
```
:::

#### Parameter Description

No parameters required for this function.

#### Usage

Creates a new thread-safe queue for inter-thread data communication.

Return Value: Returns a queue object with the following methods: `queue:push(value)` push a value to the queue, `queue:pop()` pop a value from the queue, `queue:empty()` check if the queue is empty

::: tip NOTE
Queue is the primary way for inter-thread data communication and supports cross-thread access.
:::

### Example

```lua
import("core.base.thread")

function callback(event, queue)
    print("starting ..")
    while true do
        print("waiting ..")
        if event:wait(-1) > 0 then
            while not queue:empty() do
                print("  -> %s", queue:pop())
            end
        end
    end
end

function main()
    local event = thread.event()
    local queue = thread.queue()
    local t = thread.start_named("", callback, event, queue)
    while true do
        local ch = io.read()
        if ch then
            queue:push(ch)
            event:post()
        end
    end
    t:wait(-1)
end
```

## thread.sharedata

- Create a shared data object

#### Function Prototype

::: tip API
```lua
thread.sharedata()
```
:::

#### Parameter Description

No parameters required for this function.

#### Usage

Creates a new shared data object for inter-thread data sharing.

Return Value: Returns a shared data object with the following methods: `sharedata:set(value)` set the shared data value, `sharedata:get()` get the shared data value

::: tip NOTE
Shared data object is the primary way for inter-thread data sharing and supports cross-thread access.
:::

### Example

```lua
import("core.base.thread")

function callback(event, sharedata)
    print("starting ..")
    while true do
        print("waiting ..")
        if event:wait(-1) > 0 then
            print("  -> %s", sharedata:get())
        end
    end
end

function main()
    local event = thread.event()
    local sharedata = thread.sharedata()
    local t = thread.start_named("", callback, event, sharedata)
    while true do
        local ch = io.read()
        if ch then
            sharedata:set(ch)
            event:post()
        end
    end
    t:wait(-1)
end
```

## thread:wait

- Wait for thread completion (thread instance method)

#### Function Prototype

::: tip API
```lua
thread:wait(timeout: <number>)
```
:::

#### Parameter Description

| Parameter | Description |
|-----------|-------------|
| timeout | Required. Timeout in milliseconds (-1 for infinite wait) |

#### Usage

Waits for the thread to complete execution. This method supports mixed scheduling with coroutines, allowing you to wait for thread completion within a coroutine.

Return Value: Returns a status code indicating the wait result

### Example (Mixed Thread and Coroutine Scheduling)

```lua
import("core.base.thread")
import("core.base.scheduler")

function thread_loop()
    import("core.base.thread")
    print("%s: starting ..", thread.running())
    for i = 1, 10 do
        print("%s: %d", thread.running(), i)
        os.sleep(1000)
    end
    print("%s: end", thread.running())
end

function coroutine_loop()
    print("%s: starting ..", scheduler.co_running())
    for i = 1, 10 do
        print("%s: %d", scheduler.co_running(), i)
        os.sleep(1000)
    end
    print("%s: end", scheduler.co_running())
end

function main()
    scheduler.co_start_named("coroutine", coroutine_loop)
    local t = thread.start_named("thread", thread_loop)
    t:wait(-1)  -- Wait for thread completion in coroutine
end
```
