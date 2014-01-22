---
layout: post
title: "Web Stream Programming"
date: 2014-01-22 13:15
comments: true
categories: ["stream","web","functional programming","react"]
---

# Stream Programming

The world with time is continuative, but right now we programming in a static world with status. How to process events in realtime, or file to large for a server? Currently, we can use hadoop to process large file or storm to process stream files by distribution. But not all things ask process all data in a time, but ask process data in time.

So, we need use Stream programming, take data as stream, but not static object. [Play! framework Enumerator/Iteratee](http://www.playframework.com/documentation/2.1.1/Iteratees) which learn from [haskell Iteratee pattern](http://www.haskell.org/haskellwiki/Iteratee_I/O) make it possible.


The stream process like this:
continuative input(events, big data) -> stream process (programming) -> realtime display

Let's image there are continuative input from outside, we use realtime heap usage for demo.
And display the realtime usage in page.

![monitor](https://www.evernote.com/shard/s17/sh/e42508d5-307c-4a7f-9037-bbfd612c64a3/0b7bd7aa3fcc4afa7cad47fbcf201c4f/deep/0/web-stream-programming.png)

 
### Continuative input

Simulate continuative input: heap usage

`Enumerators` would product data, and play! provide some help to generate Enumerator from data source, such as `generateM` and `fromCallback1` .



``` scala
  val getHeap = Enumerator.generateM {
    Promise.timeout(
      Some(Runtime.getRuntime().freeMemory() * 100 / Runtime.getRuntime().totalMemory()).map(_.toString),
      1000, TimeUnit.MILLISECONDS)
  }
```
  
### Stream process: 

A `Enumerator` can compose another `Enumerator` to deal with the stream, here we compose stream to generate Http EventSource message.

``` scala
  def memory =  Action {
    Ok.chunked(Streams.getHeap &> EventSource()).as(EVENT_STREAM)
  }
```

That's all we need to do in backend by play!

remeber add a url for this api in routes
```
GET     /memory                     controllers.Application.memory
```
  
### Realtime display
There is [a nice google style gauges using d3.js](https://gist.github.com/1499279/d0a29913f447537feadbadc71f2827c6b103938a), import it, and use EventSource to recieve http chunk.

We just create a memory gauge, and listen on EventSource `onmessage` event 

``` javascript
var memoryGauge = createGauge("memory", "Memory");
var memory = new EventSource("/memory");
            memory.onmessage = function(msg) {
                var data = msg.data.replace("'","").replace("'","");
                memoryGauge.redraw(Math.floor(data));
            };
```

That's all we need to do stream programming by Play! Framework with Http5 EventSource, and D3           

The whole demo code is [here](https://github.com/XuefengWu/web-stream-programming)

## Keep going
1. I think play! [Enumerator/Iteratee](http://www.playframework.com/documentation/2.1.1/Iteratees) is good and make things possible. If want do more stream programming, need introduce [Rx framework](https://github.com/Netflix/RxJava) .
2. There is [facebook react](facebook.github.io/react/), which is good at deal with events for display.
3. [reactive manifesto](http://www.reactivemanifesto.org/)