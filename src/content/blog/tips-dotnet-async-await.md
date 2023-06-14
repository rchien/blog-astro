---
title: Tips on DotNet async await
author: Richard Chien
pubDatetime: 2016-01-31
postSlug: tips-on-dotnet-async-wait
featured: false
draft: false
tags:
  - c#
  - async
  - .net
description: "Tips on .Net async/await"
---

C# async/await is introduced around 2012 as part of C# 5.0 syntax. Yes I know it’s 2015, a whole 3 years since then. In Internet time this is like eternity.

## Why yet another post on async/await?

AFAIK the Internet is already saturated with resources on the topic but what I have yet to come across is short summary of tips or key concepts that may be illusive even to senior .Net developers (i.e. yours truly and friends). More importantly, this is a post to remind myself the essence of async/await and valuable lessons that I learned through this experience.

## Lessons learned

A few posts back, I had blogged about [Thrift](https://richardchien.azureedge.net/tips-on-net-async-await). At that time I had strong dislike for its generated async code but I had a hard time quantifying my dislikes. Determined to correct that, I decided to do a deep dive on async/await and boy was I naive thinking that I had understood async/await. I’m no stranger to concepts of async/await. I had watched numerous videos and have read codes, and done few exercises. I could even recite the key mantra paraphrased by most presenters on this topic “…there’s no thread…”. But I did not try to teach the topic, did not do dive deep and did not think deeply about it. In the end, I did not internalize the async/await concept.

Doing a deep dive has helped me realize that

1. Learn by teaching. Teaching is not restricted to one-on-one in person.
   Teaching could be in a form of blog post where I try to summarize and
   simplify the concept with my own words.
2. No matter how well I think I know about something, be humble. There’s always a chance that I am wrong.

Now on with the fun technical stuff. There’s just one thing we need to do - define what task represents. The one on [MSDN](https://msdn.microsoft.com/en-us/library/system.threading.tasks.task%28v=vs.110%29.aspx) is not very good.

### `Task` or `Task<T>`

Task is an overloaded term in .Net. Personally I believe it’s best to think of Task as an abstraction that represents a unit of work that that can be in various states. Some of the key states
are completed, running (aka hot task), not started (cold task). I consider these pillar states for Task. See [here](<https://msdn.microsoft.com/en-us/library/system.threading.tasks.taskstatus(v=vs.110).aspx>) for the complete list. Tasks can be scheduled to run on either worker thread or main UI thread. In fact `Task.Run(…)` uses default TaskScheduler which queues the Task to execute on thread obtained through ThreadPool.

> If you are familiar with future. Task is like future and TaskCompletionSource is like promise.

It’s important to realize that while TPL (Task Parallel Library) and TAP (Task Async Pattern) both revolves around Task, consumption of Task can be quite different depending on context. Stephen Cleary explains it best on the differences [here](http://blog.stephencleary.com/2014/04/a-tour-of-task-part-0-overview.html).

Armed with a good definition of Task, we should then be able to rationalize how Task bridges async and parallel computing. I won’t even attempt to cram the discussion into this post. That’s a future future post.

## Tips

Note that I will not attempt to cover async/await syntax. See Google or [resources](https://richardchien.azureedge.net/tips-on-net-async-await) for some that I find helpful. What this post provides is write-up on key concepts in point form with links for in-depth and extended reading. Here are my personal top 9 most important concepts/tips/gotchas. The selection and ranking is mostly empirical based on my and my colleagues’ experience. The only remotely scientific part is me Googling to see frequency of questions asked and quantity of answers found.

1. No extra thread

Async operations, usually I/O or network operation, do not involve extra threads. See Stephen Cleary’s [excellent explanation](http://blog.stephencleary.com/2013/11/there-is-no-thread.html). It’s dangerous to assume program being single-threaded whenever we see async and Task. As mentioned in [What task represents](https://richardchien.azureedge.net/tips-on-net-async-await), Task is also used in TPL so multi-threading might be involved. In mobile app, async and parallel computation are often intertwined. Pay close attention to how the Task is obtained.

1. Await does not block
   `await Task<T>` is non blocking. Compiler essentially extracts the rest of code beginning from await statement to separate code block (called continuation) and sign up continuation for
   execution **after** async call returns.

```csharp
Task<string> unicornTask; //pretend we obtained this through some xxxAsync(…) method
String str = await unicornTask;
`Console.WriteLine(str);`
```

Is roughly equivalent in spirit to

```
Task<string> unicornTask; //pretend we obtained this through some
var syncContext = SycnchronizationContext.Current;
unicornTask.ContinueWith(
    (t) =>
    {
        Action continuationBlock = () =>
        {
            string result = t.Result();
            Console.WriteLine(result);
        };

        if (syncContext != null)
            syncContext.Post(continuationBlock, null);
        else
            Task.Run(continuationBlock);
    `});`
```

Of course the actual code is a lot more complicated and things get tricky when you have multiple await, nested await or await in a loop.

1. Avoid creating sync or async method wrapper

`sync:`
Do not call `task.Wait()` to create synchronous wrapper of async code. This is a common cause of deadlock on program with UI thread. Surprisingly, console apps are immuned to this.

`async:`
`Task.Run(…)` does not produce true async code. We are offloading work to another thread. Ultimately we are still burning a thread to execute the code. It’s best to leave the threading decision to caller. Imagine the extreme case where every method call of your API tries to grab a thread from the ThreadPool.

1. Watch out for use of Task.Result and Task.Wait()

Task.Result blocks until result is available. It’s equivalent to calling Task.Wait(). Both cases may cause deadlock. See [here](http://blog.stephencleary.com/2012/07/dont-block-on-async-code.html) for detail.

1. async will not help with CPU-bound operations
   For CPU-bound operations, consider [data parallelism](<https://msdn.microsoft.com/en-us/library/dd537608(v=vs.110).aspx>) or [task parallelism](https://msdn.microsoft.com/en-us/library/dd537609%28v=vs.110%29.aspx). In .Net we’ll be using Task as well but there shouldn’t be any await.
2. async improves responsiveness in client and scalability in server
   In client code, we free up the UI thread not waiting for async operations to complete. This allows much more responsive UI on either mobile or desktop. In server code, we free up worker threads to service other http requests. This allows the server to scale better for the midnight shopping madness. Be careful that we could have different threads serving same http requests. See [here](https://msdn.microsoft.com/en-us/magazine/dn802603.aspx) for more detail.
3. Think twice about having `public void async SomeMethodAsync(…)`
   As we [defined earlier](https://richardchien.azureedge.net/tips-on-net-async-await), Task is an abstract representation of work. Returning void forfeits the ability to track progress. This is akin to fire-and-forget. We seldom want this behavior so think twice about having such method signature. One common scenario that I’ve seen are async event handler code like mouse click that triggers content refresh. The mouse that triggers the event do not care and cannot track the progress of task.
4. Avoid Thread.Sleep(…) in async method, use Task.Delay
   Use Thread.Sleep will cause the entire program to freeze up. Remember [there’s no extra thread](https://richardchien.azureedge.net/tips-on-net-async-await)? Use `await Task.Delay` if you want to introduce artificial delay in async method. Underneath the cover, Task.Delay uses `System.Timer.Timer`.
5. Use ConfigureAwait(false) to improved performance and avoid deadlock
   By default .Net runtime will attempt to marshal the continuation back to original context captured. This is especially useful for client programs with UI thread. However, for novices, this can be a common source of deadlock. Use ConfigureAwait(false) to allow continuation to run in a separate thread. Make sure there are no code that touches UI in continuation block.

IMHO, async/await is no more than super advanced syntactic sugar. It makes very readable async code but in the end we still need to be aware of its limitations. Here are the resources I use learning about async/await. I would recommend anything from Stephen Toub or Stephen Cleary.
