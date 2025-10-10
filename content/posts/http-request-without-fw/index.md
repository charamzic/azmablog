+++
title = "HTTP Request Without FW"
description = "One of my previous blog articles on basic Java"
date = 2024-02-27
[taxonomies]
tags = ["java", "archive", "http"]
[extra]
banner = "http.jpg"
toc = true
toc_inline = true
+++

As part of learning the basics, I was exploring Java’s core libraries and thought I’d try writing a minimal HTTP request.
Lately, I’ve been enjoying digging into the fundamentals — I want to better understand what we skipped during my retraining in favor of getting things working as quickly as possible.

During the retraining, we jumped straight into Spring Boot before really knowing what was going on.
Sure, we got some explanation, but even after hearing the difference between Spring and Spring Boot, it didn’t really click for me back then — let alone understanding what’s happening under the hood.

That’s why I’m trying to write and build as much as possible directly in the terminal.
And to figure out how to do the most with the least.

---

### What does a bare-bones HTTP request in Java look like?

I only needed the [java.net](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/net/package-summary.html) package.
The only tricky part at the end was parsing JSON and extracting just the relevant part of the response body — because there’s no JSON parser in the core libraries.
(Or at least, I didn’t find one.)
But what *is* there? The good old regex matcher — and that did the job.

I started by importing what I needed. As I said, I’m writing in Vim with no plugins or LSP. Nothing auto-completes for me.
It’s good practice — and in this case, not even that hard.
To send an HTTP request and receive a response, I only need four imports from `java.net`.

```java
import java.net.URI;                    // Uniform Resource Identifier object — needed to identify a web resource
import java.net.http.HttpClient;        // client to send the request and receive the response
import java.net.http.HttpRequest;       // objects for the request and response
import java.net.http.HttpResponse;
```

---

### The main part of the program looked like this

I defined the client and request and picked one of the many open APIs available online.
This endpoint returns a random activity each time you call it — something to do when you’re bored:
`www.boredapi.com/api/activity`.

Then I declared the response object, and inside a `try` block, sent the request. 🙂

```java
HttpClient httpClient = HttpClient.newHttpClient();
        
HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create("https://www.boredapi.com/api/activity"))
        .GET()
        .build();

HttpResponse<String> response = null;

try {
    response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());

    System.out.println("Response Code: " + response.statusCode());
    System.out.println("Response Body: " + response.body());
} catch (Exception e) {
    e.printStackTrace();
}
```

---

### HttpClient

`HttpClient` lets you send either a synchronous or an asynchronous request — see the [documentation](https://docs.oracle.com/en/java/javase/21/docs/api/java.net.http/java/net/http/HttpClient.html).
I chose the synchronous one, which means the program pauses until it receives a response (or times out — you can define a timeout too).

I got my response, and since I printed it right away along with the status code, here’s what it looked like:

```
Response Code: 200
Response Body: {"activity":"Donate to your local food bank","type":"charity","participants":1,"price":0.5,"link":"","key":"4150284","accessibility":0.8}
```

A noble activity, right?
As you can see, the response contains more than just the activity — there’s also the type, number of participants, cost, etc.

---

### There is no JSON parser, Neo…

I was only interested in the activity itself, and I realized there wasn’t an easy way to extract it from the raw string.
Sure, I could’ve messed around with substrings, but that felt clumsy — and what if the response format changes next time?
I didn’t like that idea. I couldn’t think of a clean solution at first.

So I looked through the core libraries again and found these two useful imports:

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;
```

---

### Regex

Regex still feels like advanced wizardry to me, but I know the basics.
Here’s the pattern I used: `"activity"\\s*:\\s*"(.*?)"`.

1. Match the exact `"activity"` string, including quotes.
2. Then zero or more spaces `\s*`.
3. Followed by a colon `:`.
4. And again, zero or more spaces `\s*`.
5. Then the opening quote of the value I want `"`.
6. Followed by any number of characters — but as few as possible — `(.*?)`, grouped.
7. And finally, the closing quote `"`.

When properly escaped in Java, the code looks like this:

```java
Pattern treasurePattern = Pattern.compile("\"activity\"\\s*:\\s*\"(.*?)\"");
```

Then I passed it to the matcher:

```java
Matcher matcher = treasurePattern.matcher(response.body());

while (matcher.find()) {
    String value = matcher.group(1);
    System.out.println("Activity  = " + value);
}
```

And the result:

```
Activity  = Donate to your local food bank
```

---

## Tip to wrap up

I compiled the program in the terminal — either the classic way with the JDK compiler:
`javac <class name>.java`
or directly from Vim with `:make %`.

I ran it with:
`java <class name>`

If you want the program to be a bit more talkative and see what’s going on behind the scenes, try:
`java -verbose <class name>`

A follow-up Randori post is [Asynchronous HTTP Request](/posts/asynchronous-http-request).

