---
layout: post
title:  "关于AsyncTask造成阻塞问题"
categories: android,tiny
author: "killnono"
---



###AsyncTask应用
AsyncTask的应用Doc上写的比较明确，用于独立与UI主线程处理一些耗时操作，防止阻塞。
一般集成后，重写backgroud的操作和操作结束后postExe刷新通知UI线程。
相对于Handler，更下一个异步的处理机制。

###AsyncTask阻塞的问题
同事开启执行多个AsyncTask任务时，比如A的doingbackground中实现了一个while无线循环的任务<比如隔几秒检查的Watcher任务或是循环任务>
发现在A之后加入的AsyncTask任务，都堵塞住了. 

````java    
protected Void doInBackground(Object... params) {
        while (true) {
            //do someting
        }
    }
````

过了遍源代码，发现AsyncTask的机制是一个全局的异步队列，每次值执行一个任务。
所以A没执行完，A后面的任务都是出于等待状态。

###解决问题
android  关于asyncTask的 doc文档：
>Order of execution
When first introduced, AsyncTasks were executed serially on a single background thread. Starting with DONUT, this was changed to a pool of threads allowing multiple tasks to operate in parallel. Starting with HONEYCOMB, tasks are executed on a single thread to avoid common application errors caused by parallel execution.
If you truly want parallel execution, you can invoke executeOnExecutor(java.util.concurrent.Executor, Object[]) with THREAD_POOL_EXECUTOR.


AsyncTasks在执行是，在单个后台线程中有序执行。DONUT版本后改成线程池了，HONEYCOMB后，默认又改成单个后台线程有序执行，也加入了对线程池的支持**executeOnExecutor**
####We can do it like this

````java
 Executor exec = new ThreadPoolExecutor(5, 10, 10,
                TimeUnit.SECONDS, new LinkedBlockingQueue<Runnable>());
        mTask.executeOnExecutor(exec);
````

###Tips:
看文档是还看到关于其线程规则：
####**Threading rules**
There are a few threading rules that must be followed for this class to work properly:
 
+ The AsyncTask class must be loaded on the UI thread. This is done automatically as of JELLY_BEAN.
+ The task instance must be created on the UI thread.
execute(Params...) must be invoked on the UI thread.
+ Do not call onPreExecute(), onPostExecute(Result), doInBackground(Params...), onProgressUpdate(Progress...) manually.
+ The task can be executed only once (an exception will be thrown if a second execution is attempted.)

task需要实例化和调用必须载UI线程中,虽然还没碰到该问题，可以在出问题是作为error排查~

