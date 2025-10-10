+++
title = "Asynchronous HTTP Request"
description = "One of my previous blog articles on basic Java"
date = 2024-03-02
[taxonomies]
tags = ["java", "archive", "http"]
[extra]
toc = true
toc_inline = true
+++

~First of all, for those who prefer video, here you go. Toward the end, there are a few extra bits I don’t mention in this article — for example, a Java 21 preview feature and how to save the program call as an alias.~

> [!NOTE]
> I am recycling a few of my older posts from my previous blog to avoid losing them completely. However, the videos are gone; my YouTuber career didn't last long.

---

Last time, I wrote about a [Synchronous HTTP Request](/posts/http-request-without-fw), and today I’ll try to pimp it up a bit and send multiple requests at once.
The Java library is the same — the approach is just slightly different.

I switch between using *request* and *požadavek* (which both mean the same thing). Sorry about that — I write whatever comes naturally to my fingers. Just take them as synonyms here.

With a synchronous request, the program stops and waits for a response. It’s a good idea to set a timeout for the request so you’re not waiting until Christmas.
That’s why it’s called a *blocking* request.

With an asynchronous (non-blocking) request, the program sends it and immediately returns a `CompletableFuture` object without waiting for a response.
You can then attach callbacks and define how the program should behave once the response arrives — or doesn’t…

### Asynchronous HTTP Request in Java

As you’ll notice, I’m not handling every possible scenario here. In practice, you’d want to be more careful, but for some quick home testing, this punk version will do.

#### Imports

On top of what I imported last time:

```java
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
```

I also need to add:

```java
    import java.time.Duration;                          // because this time I set a timeout
    import java.util.ArrayList;                         // I collect responses into a list
    import java.util.concurrent.CompletableFuture;      // the object returned by async calls
    import java.util.concurrent.ExecutionException;     // in case something goes wrong
```

#### Program Arguments

I wanted the option to decide how many requests to send when running the program.
That’s easy enough to do by passing an argument, like `java <filename> <arg>`.
So, right at the start, I make sure the user actually provided one.
If not, the program exits and explains how to use it.

```java
    if (args.length < 1) {
        System.out.println("Please, add desired amount of requests.");
        System.out.println("Usage: java <file name> <number>");
        return;
    }
```

#### Creating the HTTP Client

This part is the same as with the synchronous request, except for the new timeout setting.

```java
    HttpClient client = HttpClient.newHttpClient();

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://www.boredapi.com/api/activity"))
            .timeout(Duration.ofMinutes(1))
            .GET()
            .build();
```

#### Sending Multiple GET Requests

As I mentioned above, an asynchronous request returns a `CompletableFuture` object.
The `for` loop handles the number of iterations, which I’ll specify when running the program.

```java
    ArrayList<String> results = new ArrayList<>();      // where I’ll store the response bodies
    int numberOfRequests = Integer.parseInt(args[0]);   // could use some validation here

    // Generics and arrays trigger a compiler warning: [unchecked] unchecked conversion
    CompletableFuture<Void>[] responseFutures = new CompletableFuture[numberOfRequests];

    for (int i = 0; i < numberOfRequests; i++) {
        responseFutures[i] = client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
                    .thenApply(HttpResponse::body)
                    .thenAccept(responseBody -> {
                        results.add(responseBody);
                    })
                    .exceptionally(e -> {
                        e.printStackTrace();
                        return null;    // if the above function throws, it’s caught here and the CompletableFuture becomes null
                    });

        System.out.println("The " + (i + 1) + ". request has been sent.");
    }
```

Then I just wait for all responses to finish before the program exits,
and print out the strings I stored.

```java
    CompletableFuture.allOf(responseFutures).join();

    System.out.println("\nHere are the results:");

    for (String s : results) {
        System.out.println(s);
    }
```

#### Result

It works!
Here’s the output when I forget to specify the number of iterations:

```bash
    > java AsyncApiCall 
    Please, add desired amount of requests.
    Usage: java <file name> <number>
```

And here’s what it looks like with three requests:

```java
    > java AsyncApiCall 3
    The 1. request has been sent.
    The 2. request has been sent.
    The 3. request has been sent.

    Here are the results:
    {"activity":"Learn how to fold a paper crane","type":"education","participants":1,"price":0.1,"link":"","key":"3136036","accessibility":0.05}
    {"activity":"Go see a Broadway production","type":"recreational","participants":4,"price":0.8,"link":"","key":"4448913","accessibility":0.3}
    {"activity":"Watch a movie you'd never usually watch","type":"relaxation","participants":1,"price":0.15,"link":"","key":"9212950","accessibility":0.15}
```

This time I didn’t bother extracting just the `activity` field,
so you can see the full response body here.
If you’re curious how to grab only the activity using regex,
check out the [post about the synchronous request](../http-request-bez-fw).

See you next time!

