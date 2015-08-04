title: Gentle introduction to Completable Future
date: 2015/06/18 12:00
authorId: BMU
---

### Introduction

There is the trend in last years chasing the high application availability. It's really rare to see our application working in vacuum, more often it's using another couple of services its depends on, aggregating and transforming their contents. And sloppiness any of these could really hurt performance of the whole. The last thing you want in this situation is to leave your application waiting to response from slow service and waste the valuable cpus cycles and blocking threads. The Java language try hard to provide us with tools of trade: Fork/Join framework was added to Java 7 and Parallel Stream to Java 8. All of them help us to be more productive working in highly concurrent environment by creating the right level of abstraction that makes our work more easy and less error prone. In this article i'll try to introduce you the new concept of CompletableFuture in Java 8.

<!-- more -->

### Practice

<code>Future</code> is a placeholder for a result of function that will become available at some point in the future. It is asynchronous result and it provides us a way to reference something that not available yet. It is read only and could not be changed from outside.

Here is a simple use case for <code>Future</code>:

```Java
Future<Integer> priceA = callSlowService();
Future<Integer> priceB = callSlowService();
try {
    Future<Integer> afterDiscountA = applyDiscount(priceA.get());
    Future<Integer> afterDiscountB = applyDiscount(priceB.get());
    return new FinalPrice().add(afterDiscountA.get()).add(afterDiscountB.get());
} catch (InterruptedException | ExecutionException ex) {
    ex.printStackTrace();
}
```

However, futures are rather limited. Probably you can spot the problem here. If you want to pass result of <code>callSlowService()</code> operation further, you have to block thread of execution with <code>get()</code> method(yep, you are right - it is some sort of polling). Another problem is that slow operation could never return. And it could be potential bottleneck. Instead of executing operations in parallel we need to wait on result of first operation. The CompletableFuture here to help.

CompletableFuture was introduced in Java 8. Also it was available even before Java 8 in Guava or Spring Framework as the <code>ListenableFuture</code>. And here just few examples what we can do with CompletableFuture:

1. Combine several asynchronous operation
2. Waiting for task completion
3. Listen to Future completion and react to it success or error completion
4. Chaining results of dependent futures

And many more. For more info check JavaDocs for [CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) and [CompletionStage](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletionStage.html).

To better describe this concept I have created simple [application](https://github.com/elqsar/completablefuture-examples) you can follow. The application simulate pretty slow API calls and how we can fight with it.

Application initial setup:

* List of 9 shops
* Api call delay is one second(you could change it in <code>SlowNetwork.java</code>)
* And we should collect the prices and discounts from all these shops

And here is my machine specification:
<code>MacBook Pro 2013, 2,4 GHz Intel Core i5 8 GB 1600 MHz DDR3</code>

> I've used a very simple micro benchmark in the rest of examples. If you want to get more advanced and precise result, you could try [JMH tool](http://openjdk.java.net/projects/code-tools/jmh/).

There is a first step. <code>getPrice()</code> method query sequentially and then apply discount to it. Pretty boring. And result is **18135** ms.

```Java
public static List<PriceRecord> findPricesBlock() {
    return shops.stream()
            .map(Shop::getPrice)
            .map(Discount::applyDiscount)
            .collect(toList());
}
```

If you comfortable with Stream API you could guess how we can improve it with very little effort.By using the parallelStream. And this small change improve final result to **6051** ms. Good job!

```Java
public static List<PriceRecord> findPricesParallel() {
    return shops.parallelStream()
            .map(Shop::getPrice)
            .map(Discount::applyDiscount)
            .collect(toList());
}
```

Let's see if we could do better with CompletableFuture. In the next step we create it with factory method <code>supplyAsync(...)</code>, so our code will be executed in different thread. After this we want to apply discount to all prices, also in separate thread and <code>thenCompose(...)</code> method do heavy lifting for us. People who are familiar with [Scala Futures](http://docs.scala-lang.org/overviews/core/futures.html) could recognize that this one very similar to <code>flatMap</code>. Also notice we have two separate streams here.Becasue of lazy nature of streams all requests to Shop API would be executed sequentially if we were using pipeline instead.
 
> Most of CompletableFuture methods used in this article are non-blocking.

*Note:* <code>join</code> method here is pretty similar to <code>get()</code> one. The only difference is an exeption it could throw is unchecked.

```Java
public static List<PriceRecord> findPricesCF() {
    List<CompletableFuture<PriceRecord>> futures = shops.stream()
            .map(s -> CompletableFuture.supplyAsync(s::getPrice))
            .map(f -> f.thenCompose(p -> CompletableFuture.supplyAsync(() -> Discount.applyDiscount(p))))
            .collect(toList());
    return futures.stream()
            .map(CompletableFuture::join)
            .collect(toList());
}
```

Lets run it and... you will be a little disappointed. The execution could be more slower comparing with parallel stream one from step Two or could be the same(it is **6055** ms on my machine). And here is the reason. Both solutions use the<code>ForkJoinPool.commonPool()</code> under the hood and use the number of threads equal to number of available processors: <code>Runtime.getRuntime().availableProcesssors();</code> So let's see if we can do better with custom threadpool.

Here is our custom executor based solution. First we create the thread pool and then pass it to CompletableFuture operation as a parameter. And result is **2034** ms. So we completed a long way from more than **18000** ms to **2000** ms.

```Java
public static ExecutorService es = Executors.newFixedThreadPool(Math.min(shops.size(), 100), r -> {
    Thread thread = new Thread(r);
    thread.setDaemon(true);
    return thread;
});

public static List<PriceRecord> findPricesCustomExecutor() {
  List<CompletableFuture<PriceRecord>> futures = shops.stream()
          .map(s -> CompletableFuture.supplyAsync(s::getPrice, es))
          .map(f -> f.thenCompose(p -> CompletableFuture.supplyAsync(() -> Discount.applyDiscount(p), es)))
          .collect(toList());
  return futures.stream()
          .map(CompletableFuture::join)
          .collect(toList());
}
```
Another useful pair of methods <code>thenAccept/thenRun</code>, which you could use as event listener for Future completion. It is shown in last fifth step of my example.

### Summary

And it is the end of examples but not <code>CompletableFuture</code>. It contains the much more useful methods inside, for example for asynchronous exeption handling, combining futures together, waiting for completion and much more. In this small article i've just introduced the most common and it's up to the reader to explore it further. 

Hints:

* Always use overloaded version of <code>get(long, TimeUnit)</code> method of <code>CompletableFuture</code> and provide timeout to avoid situation you have blocked client forever.

* Try to figure out what you really want, may be simple <code>paralleStream()</code> is just more suitable in your case.

* Remember ThreadPool size formula:

```
Nthreads) = Ncpu * Ucpu * (1 + W/C)

Ncpu - number of cpus
Ucpu - cpu utilization
W/C - ration of wait to compute time
``` 