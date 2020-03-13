---
title: "Go: Share Memory By Communicating"
date: 2017-06-26T16:34:07+08:00
tags: ["golang"]
author: "七哥"
categories: ["Golang"]
---

[原文](https://coderwall.com/p/rklk_a/go-share-memory-by-communicating)
Let's explore Go's concurrency slogan:

Do not communicate by sharing memory; instead, share memory by communicating.

- 不要依赖共享内存去通信，而是要通过通信去共享内存。[点击前往：source](https://golang.org/doc/effective_go.html#concurrency)

Before, we go ahead, let's just throw up our hands in desperation and say, "What does that even mean?!!"

- 在我们继续之前，让我们抛出我们的双手绝望的说：这特么的是什么意思；

Go's Concurrency is similar to Unix pipelines and Hoare's Communicating Sequential Processes (CSP). Since we are are talking about sharing memory, understanding the memory models for above may get us an inkling of what's going on.

- go的并发，是非常类似unix的pipilines、Hoare's 通信顺序进程(CSP).

Unix Pipelines
Ken Thompson(co-inventor of Go) added pipes to Unix systems in 1973. A unix pipe is exactly as the name would suggest: it pipes output of one program as input of the other. Each pipe has it's own pipe buffer. A process reads/writes to the pipe buffer for input/output respectively. What if there are multiple processes reading/writing on the same pipe?

- Ken Thompson，Go语言的联合开发团队中的成员，在1973年把pipes添加到Unix系统中，一个unix pipe非常像其名字建议的那样：管道化的输出一个程序作为另一个程序的输入。每个pipe有它自己的pipe缓冲区。一个进程‘读/写’pipe缓冲区作为各自的的‘输入/输出’。如果有多个进程在同一个pipe上进行读写操作，该怎么办？

Something like this:
```
$ps1|ps2
```
Here ps1 writes to the pipe, while ps2 reads it. Don't we need some kind of synchronization between the two processes? So, you know, ps2 should read the pipe only when the there is message to be read. Before we go ahead, let's define our terms here: Synchronization is an agreement between two proceses towards a certain sequence of reading/writing, Message is the data being read/written into a pipe.

- 就像这样：ps1|ps2。这里的ps1往pipe里写，ps2读取。我们难道不需要在这两个进程之间进行某些同步操作吗？你知道的，只有在有数据要被读取的时候，ps2才能从pipe里读取。在我们继续之前，让我们定义一下我们的术语：Synchronization，是两个进程间的 协议，指向一个特定顺序的读写序列。Message是正在往一个pipe里读写的数据
If we knew what the agreement was and who was the mediator we could get a better idea.From the pipe man pages:

- 如果我们知道协议是什么、谁会是协议执行的调节人，我们就能得出一个好点的主意。看一下pipe的主页：
```
 If a process attempts to read from an empty  pipe,  then read(2)  will
block  until  data  is  available.  If a process attempts to write to a
full pipe (see below), then write(2) blocks until sufficient  data  has
been  read  from the pipe to allow the write to complete.  Non-blocking
I/O is possible by using the fcntl(2) F_SETFL operation to  enable  the
O_NONBLOCK open file status flag.
- 如果一个进程试图从一个空pipe里读取，那么read(2)将会阻塞知道有可用的数据。如果一个进程试图往一个占满的pipe里写，那么write(2)将会阻塞，直到同样大小的数据被其他pipe读取走才会允许write操作完成。通过使用开启文件状态参数fcntl F_SETFL，去启用非阻塞规则时，非阻塞IO才是可用的
```
- [点击前往：source](http://unixhelp.ed.ac.uk/CGI/man-cgi?pipe+7)


So you see, ps1 is sharing memory with ps2 by communicating it a message which is syncronized as per the agreement mediated by the kernel. If, "Bogus! How is that sharing memory by communicating? You could even say that the other way around. Boo... " is what you are thinking, consider this: Neither ps1 or ps2 has the book-keeping information to enforce the agreement. The kernel is the book-keeper and the mediator. Therefore, ps1 & ps2 are not the one's who are sharing memory. Also to a user, the bird's view would be: "the message itself is the synchronizer"

- 正如你看到的，ps1与ps2通过通信共享了内存，该消息由kernel内核按照协议进行同步化。如果，“假的!通过通信去共享内存是怎么样，你甚至可以这样说。嘘……”你的想法是这样的:，无论是ps1还是ps2，都没有记录遵守协议的信息。内核是记录者和调停者。因此，ps1、ps2都不是都是不是共享内存的。通常对一个用户来说，观点就是：消息本身就是实施同步的那一个角色

Communicating Sequential Processes
CSP is a way to describe/model the specs for concurrency patterns and interactions. The constructs from CSP will help us understand Go concurrency patterns even better. Let's take a classical CSP example from wikipedia:

- 通信顺序进程（CSP），是一种描述、建模并发模式与交互的规范方法。CSP这种概念会帮助我们更好的理解Go并发模式。我们看下wiki百科上的一个CSP的例子

[Example](https://en.wikipedia.org/wiki/Communicating_sequential_processes#Examples)

In the above example the behavior of processes VendingMachine and Person depend on events coin and card. Now either we can synchronize on both events or just the coin event. In both cases, the choice will be deterministic. But to an external observer, who doesn't know about these events, i.e Person doesn't make the decision that inserting a card or coin will lead to delicious chocolate, the choice is nondeterministic. The idea(in the context of Go) is to avoid nondeterminism to reduce complexity.

- 在上面这个例子中，VendingMachine 、 Person这两个进程的行为依赖于coin/card产生的事件，在两个例子中，选择是确定的。但是对于外部观察者来说，不知道这些时间，例如一个自然人不知道插入银行卡、或硬币，将导致选择充满不确定性。在Go的上下文中，避免不确定性来降低复杂性。

We see two styles of concurrency:
```
Deterministic: sequence of actions is well defined
Non-deterministic: sequence of actions is not defined.
```
This is where Go excels. It promotes deterministic concurrency by proving well defined sequence of actions(a.k.a synchronization) namely through channels which has one sender and one reciever each.

- 我们看到并发的两种形式：确定性、不确定性。这是Go擅长的。它通过提高良好的定义操作顺序来促进并发的确定性（also known as又名同步），也就是通过拥有一个发送者、一个接受者的channel来达到目的。

For even better understanding let's dive into the syncronization agreement which Go has.

- 为了更好的理解，我们更深入Go的sync同步协议中去

Go Memory Model
The "Happens before" model clearly defines this agreement
```
- `To specify the requirements of reads and writes, we define happens before, a partial order on the execution of memory operations in a Go program. If event e1 happens before event e2, then we say that e2 happens after e1. Also, if e1 does not happen before e2 and does not happen after e2, then we say that e1 and e2 happen concurrently.`
and
`Within a single goroutine, the happens-before order is the order expressed by the program.`
```
- Gode 内存模型。之前出现的模型清楚的定义了这个所谓的同步协议：为了规定读写的需求，我们之前定义过在Go程序中，部分内存操作的执行顺序：如果时间e1在e2之前发生，那么我们可以说e2在e1之后发生，同样的，如果e1不在e2之前发生，也不在e2之后发生，那么我们说e2与e1是并发的。并且，在单个的goroutine内，发生之前的顺序是程序表达的顺序

[The Go Memory Model](https://golang.org/ref/mem)

Communicating by channels is the best way to follow the above synchronization agreement. Though the sync package provides other lower level primitives, Once and Waitgroup are the ones advised for higher level synchronization.

- 依赖channel去通信，是遵循以上协议的最好的方式，虽然sync包提供底层的基本操作，但还是建议使用非底层的同步方法：waitGroup

This approach also elucidates, share memory by communicating. Since the concurrency style is deterministic, the memory book-keeping and read/write enforcement is handled by the runtime. Not only that, the synchronization primitives have been intelligently wrapped up in a higher-level construct called, Channels. With respect to the programmer, message itself is the synchronizer which reduces complexity and spreads awesomeness.

- 这种方法也说明了“依赖通信去共享内存”，由于并发形式是确定的，内存记录、读写的执行，由runtime来处理。不仅仅是这些，同步原子操作已经被智能的打包起来成为一个高等的概念：channel，对程序员来说，消息本身就是降低复杂性的同步器

I hope, we now have a better understanding of Go Concurrency Model. Following resources can help:
- 我希望现在都对Go的并发模型有了更好的理解，以下文章也有帮助：
```
http://golang.org/doc/codewalk/sharemem/
http://blog.golang.org/2010/07/share-memory-by-communicating.html
http://talks.golang.org/2012/concurrency.slide
```