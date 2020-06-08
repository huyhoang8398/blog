---
title: "Concurrency in Go"
date: 2020-05-13T06:45:17+07:00
draft: true
author: "kn"
tags: ["Go", "Concurrency"]
cover: "/posts/concurrency-in-go/images/cover.jpg"
keywords: ["Go", "Concurrency"]
description: "Concurrency in Golang"
showFullContent: false
---

Concurrency is a really big part of Go and also a big selling point which is a reason why a lot of people chose the language.

## Introduction

Concurrency is not quite the same as parallelism, to run things in parallel means to run two things at exactly the same time - this is what happens on a multi-core processor you that you have one core doing one thing and another core doing a different thing ( both simultaneously ).

Typically through the lines of code that make up a program have to run in the right order which makes it hard to parallelize and execute two lines at the same time so concurrency is about breaking up a program into independently executing tasks that could potentially run at the same time and still getting the right result at the end.
So a concurrent program is one that can be parallelized. 

In this article, we are not going to concern ourselves with what is happening at the CPU level and whether something is running on multiple cores or not, the Go runtime and the operating system will take care of it for us. We can concentrate on the structure of our program and using the tools that Go gives us to make our code concurrent.

## Simple example
This is a very simple function called count, it will take a string as an argument and in an infinite for loop that counting up, Im gonna output the number that we are at and the string that we passed in and then sleep for half a second.
And then I will make a call to count to "fish" and "sheep". So this is a synchronous program and there is no concurrency.

```Go
package main

import (
    "fmt"
    "time"
)

func main() {
    count("sheep")
    count("fish")
}

func count(thing string) {
    for i := 1; true; i++ {
        fmt.Println(i, thing)
        time.Sleep(time.Millisecond * 500)
    }
}
```
If we run this, it is going to execute the first count function and it's gonna wait for it to finish before moving on to the next line but the `count("sheep")` never finishes so it's just gonna count sheep forever and never get to the fish.

But if we call the function with the word `go` in front of it (`go count("sheep")`), it will not wait for it to finish before moving on to the nxt line.

It will say go and run this `count("sheep")` function in the background and then continue to the next line immediately and this creates what is called `go routines` and that `go routines` will run concurrently so we now actually have two go routines - The main function with the main execution path of the program and `count("sheep")` that we've created explicitly so these will both run side by side.

**Output**

![Simple Count program output](/posts/concurrency-in-go/images/count.png)

Go routines are very efficient, it's okay to make a hundred even thousand of go routines but bear in mind that you cant make a program infinitely fast by adding more and more concurrent go routines because ultimately you are constrained by how many cores that your CPU has.

What if we both run count function as go routines?

Now you might expect this to do exactly the same as before and you'd be almost right but we get a very different result, we dont get anything :))

In Go when the main go routine finished, the program exits, regardless of what any other go routines might be doing.

Previously the main go routine never finishes because it would have an infinite for loop in it but now we've pushed that loop into its own go routine so the main function will continue immediately to the next line, but there are no more line of code so it's done and the program terminated and the go routines that we've created ourselves haven't had time to do anything.

You will often see people add a call to `fmt.Scanln()` at the end of the main function to fix this problem - it will stope the main function from immediately terminating because this is a blocking call (it will wait for user input).
So this gives our go routines time to execute and it's gonna keep doing this until I press Enter.

This is not a very useful (actually useless) solution because it does require manual user input.
What we can do instead is use a wait group ( from `sync` package from standard lib).
So first I create a counter and increment it by one to say that I have one go routine to wait for.
```go
var wg sync.WaitGroup
wg.Add(1)
```

The next step is to decrement the counter when the go routine finished.
So after the for loop i want to decrement a pointer to the wait group to the count function but I dont think it's really the responsibility of count to deal with it, so instead I'm gonna wrap the call count in an anonymous function
```go
go func() {
    count("sheep")
    wg.Done()
}()

wg.Wait()
``` 
Since we've created this function inline so that we have access to that `wg` variable which is convenient and `Done` literally just decrement the counter by one.

So all we have so far is a counter of how many go routines are running, the useful now is to call `wait` at the end of the main function, this will block until the counter is zero so any go routine haven't finished it will wait.

**Final example**
```Go
package main

import (
    "fmt"
    "time"
    "sync"
)

func main() {
    var wg sync.WaitGroup
    wg.Add(1)
    go func() {
        count("sheep")
        wg.Done()
    }()
    
    wg.Wait()
}

func count(thing string) {
    for i := 1; i <=5; i++ {
        fmt.Println(i, thing)
        time.Sleep(time.Millisecond * 500)
    }
}
```
That is how to create a go routine ( really simple but not massively useful so far ), so what we need next are channels.

## Channel

A channel is a way for go routine to communicate with each other.
So far the count function has just been outputting directly to the terminal but what if we want to communicate back to the main go routine.
We can accept a channel as an argument it's like a pipe through which you can send a message or receive a message.

Channels have a type as well so we can create a `chan string` and we'll only be able to pass messages that are string, any type works though even channel through channel.
So instead of outputting thing to the terminal, Im gonna use `c <- thing` to send the value of thing over the channel.
And now we need to pass the channel in so first we can make one using `make` function and then we can use `msg := <- c` to receive a message from the channel.
So this is going to receive one message output "sheep" once and then terminate.
And it is important to understand that sending and receiving are blocking operations, when you try to receive something, you have to wait for that to be a value to receive, similarly when you are sending a message, it will wait until a receiver is ready to receive.

**Example code**

```Go
package main

import (
    "fmt"
    "time"
)

func main() {
    c := make(chan string)
    go count("sheep", c)
    msg := <- c
    fmt.Println(msg)
}

func count(thing string, c chan string) {
    for i := 1; i<=5; i++ {
        c <- thing
        time.Sleep(time.Millisecond * 500)
    }
}
```