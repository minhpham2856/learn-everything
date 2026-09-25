Async programming lets a program start a task that may take some time without forcing the program to sit there doing nothing while waiting for that task to finish. The main idea is: **start the slow operation -> let the program do other work -> continue when the operation finishes.** 

Opposite of that is synchronous programming. Imagine ordering food at a restaurant: **you order food -> stand next to the kitchen -> wait -> get food -> continue**. With async: **you order food -> sit down -> do something else -> waiter tells you the food is ready -> continue eating**. The important thing is that `async` does NOT automatically mean "run this on another thread."

# 1. Why we need it

Some operations are naturally slow because they depend on something outside the CPU:
- Reading a file
- Making an HTTP request
- Querying a database
- Waiting for user input
- Communicating with another server
- Reading from a network socket

# 2. The core idea behind async

2 most often used keywords are:
```csharp
async
await
```

For example:
```csharp
async Task DoSomething()
{
    await Task.Delay(1000);
    Console.WriteLine("Done");
}
```

`await Task.Delay(1000);` means: "Wait for this async operation to finish, but don't block the current thread while waiting."
```text
Start operation
      v
Is it finished?
   /       \
 yes        no
 v           v
continue  return control
             v
		  operation finishes
             v
          continue
```

`async` marks a method as one that can use `await`:
```csharp
async Task DoWork() { await Task.Delay(1000); }
```

But it does not itself make the method async.
```csharp
async Task DoWork()
{
    Console.WriteLine("Start");
    await Task.Delay(2000);
    Console.WriteLine("End");
}
```

Conceptually:
```text
DoWork()
   v
Print "Start"
   v
Start 2-second delay
   v
await
   |  method pauses
   |  other work can happen
   |  2 seconds pass
   v
Resume method
   v
Print "End"
```
# 3. `Task`

`Task` represents an async operation, think of  it as a promise: "I am doing something. I may not be finished yet, but I represent the eventual result of that operation."

For example:
```csharp
Task task = Task.Delay(1000);
```

The `Task` represents the delay operation. It is not the result itself. It represents the operation that will eventually complete.

`Task<T>`, on the other hand means an async operation that eventually produces a value of type `T`
```csharp
async Task<int> GetNumber() { await Task.Delay(1000); return 42; }
Task<int> task = GetNumber();
```
If we write:

```csharp
int number = await GetNumber();
```

then `await` waits for the task to complete and gives us its result. `Task<int>` then becomes `int` after `await`.

# 4. Real stuff in action

```csharp
async Task<string> DownloadData()
{
    using HttpClient client = new HttpClient();
    string result = await client.GetStringAsync("https://example.com");
    return result;
}

string result = await client.GetStringAsync(...);
```

`GetStringAsync()` returns `Task<string>` then `await` waits for the HTTP request to finish and gives us `string`


# 5. Async isn't really multithreading

```csharp
await client.GetStringAsync(url);
```

The HTTP request is mostly waiting for the network. The CPU does not need to continuously execute code during that waiting period. So there is no reason to dedicate a thread just to sitting there waiting. Async is primarily about **efficient waiting**

# 6. Async methods calling each other

```csharp
async Task<string> GetData() => await DownloadData();

async Task ProcessData()
{
    string data = await GetData();
    Console.WriteLine(data);
}

await ProcessData();
```

The async behavior can propagate upward. **async -> await -> async -> await -> async** is commonly called **async all the way** Instead of starting async code and then synchronously blocking on it with `.Result` or `.Wait()`.
# 7. async void

There are three common return forms:

```csharp
async Task
async Task<T>
async void
```

Normally we prefer `async Task` or `async Task<T>`. Avoid `async void` except for specific cases such as event handlers:
```csharp
async void Button_Click(object sender, EventArgs e) => await DoSomething();
```

# 8. Parallel async

```csharp
Task task1 = Task.Delay(1000);
Task task2 = Task.Delay(1000);

await task1;
await task2;
```

Both tasks are started before we await them; therefore they can overlap:

```text
0s              1s
|---------------|

Task 1: =========
Task 2: =========

Total = 1 second
```

Instead of:

```text
Task 1: =========
Task 2:          =========

Total = 2 seconds
```
# 9. `Task.WhenAll`

When you want to wait for multiple async operations, use `await Task.WhenAll(task1, task2);`
```csharp
async Task DoWork()
{
    Task task1 = Task.Delay(1000);
    Task task2 = Task.Delay(1000);

    await Task.WhenAll(task1, task2);

    Console.WriteLine("Both finished");
}
```
This is useful when the operations are independent.
# 10. `Task.WhenAny`

Sometimes you don't need to wait for everything. You might want themethod to continue when the first operation finishes:
```csharp
Task completed = await Task.WhenAny(task1, task2);
```
# 11. Exceptions

Exceptions from an awaited task can be caught normally:

```csharp
try { await DoSomething(); }
catch (Exception ex) { Console.WriteLine(ex.Message); }
```
# 12. Async vs Thread

A thread is an execution resource. A task represents an async operation.
```text
Thread = worker
Task = job/status of an operation
```

For CPU-intensive work, another thread may be needed to get actual parallel CPU execution:
```text
Calculate huge matrix
Compress a giant file
Process millions of numbers
```


For I/O-bound work, async allows the thread to avoid being blocked while waiting.
```text
HTTP request
Database query
File read
```

Simply:
```text
I/O -> use async
CPU -> consider parallelism / Task.Run / multiple threads
```
# 13. `Task.Run`

`Task.Run()` is commonly used to move CPU-bound work onto a thread-pool thread.

```csharp
int result = await Task.Run(() => { return CalculateHeavily(); });
```


But don't use `Task.Run()` simply because a method is async, it's usually unnecessary:
```csharp
await Task.Run(async () => { await httpClient.GetStringAsync(url); });
```

The HTTP API is already async. Wrapping it in `Task.Run()` doesn't make the it more async :))

# 14. Async in a nutshell

```text
1. Task: represents an async operation.
2. Task<T>: represents an async operation that eventually produces T.
3. async: allows a method to use await and suspend/resume.
4. await: asyncly waits for a task and retrieves its result.
```

The most important pattern is:
```csharp
async Task<T> Method()
{
    T result = await DoAsync();
    return result;
}
```

Also:
```text
async != automatically another thread
async = don't block while waiting for an async operation
```

Or remember:
```text
synchronous: Order food -> stand there waiting -> receive food
async: Order food -> do something else -> food is ready -> continue
```