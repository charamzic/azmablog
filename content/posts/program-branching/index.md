+++
title = "Program Branching"
description = "One of my previous blog articles on basic Java"
date = 2024-03-09
[taxonomies]
tags = ["java", "archive", "http"]
[extra]
toc = true
toc_inline = true
+++

~As always, here’s the video. I look half asleep and mumble a bit, but it’s beautiful anyway!~

> [!NOTE]
> I've deleted my Youtube channel, no more videos available 😶

In this video, I continued from [the previous post](/posts/http-request-without-fw) about a synchronous HTTP request.
This time, I added a simple user input — the user can choose which API call the program will make.
A small change, but it makes things a bit more fun.

For the second endpoint, I created a tiny simulated database on localhost. So in this episode, we’ll try building a mini server.
Next time, I’ll take it one step further, as I mention at the end of the video.

## Java server

I started with an app that serves as a simple HTTP server returning some data on request.

### Main method

As for libraries, I’m still sticking to the basics:

```java
import com.sun.net.httpserver.HttpServer;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;

import java.io.IOException;
import java.io.OutputStream;
import java.net.InetSocketAddress;
import java.util.Date;
import java.util.ArrayList;
import java.util.List;
import java.util.StringJoiner;
```

The main method creates a server listening on port 8080,
with a single available endpoint `/data`.

```java
public static void main(String[] args) throws IOException {
    HttpServer server = HttpServer.create(new InetSocketAddress(8080), 0); 

    server.createContext("/data", new DataHandler());
    server.setExecutor(null);
    server.start();

    System.out.println("Server started on port 8080...");
}
```

### HttpHandler

The class implementing the `HttpHandler` interface handles incoming `GET` requests.
That’s what the overridden `handle()` method does.

It receives an `HttpExchange` object.
Then it checks if the request method is `GET`, prepares the data and headers,
and tries to send back a response.

I didn’t bother with covering every edge case here —
if it’s a method we don’t expect, it just returns status code `405 Method Not Allowed`.
That simply means the server understands the request but doesn’t have an appropriate response for it.

```java
@Override
public void handle(HttpExchange exchange) throws IOException {
    if ("GET".equals(exchange.getRequestMethod())) {

        String responseData = getUsersAsJson();
        
        exchange.sendResponseHeaders(200, responseData.getBytes().length);

        try (OutputStream os = exchange.getResponseBody()) {
            os.write(responseData.getBytes());
        }
    } else {
        exchange.sendResponseHeaders(405, -1);
    }

    exchange.close();
}
```

## Adding user input

Once the server was running and tested, I moved on to modifying the client program
to send different requests based on user input.
Here’s what the full class looked like:

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Scanner;

public class ContinuousApiCall {

    public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in); // read user input

        while (true) { // could be a do-while too, right?
            System.out.println("\nChoose an option:\n1. Get a random activity\n2. Get users\nType 'exit' to end the program\n");

            String userInput = scanner.nextLine();

            if (userInput.equalsIgnoreCase("exit")) {
                break;
            }

            int userChoice = Integer.parseInt(userInput);

            String apiUrl = (userChoice == 1)
                ? "https://www.boredapi.com/api/activity"
                : "http://localhost:8080/data";

            HttpClient httpClient = HttpClient.newHttpClient();
            
            HttpRequest request = HttpRequest.newBuilder()
                    .uri(URI.create(apiUrl))
                    .GET()
                    .build();

            try {
                HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());

                System.out.println("Response Code: " + response.statusCode());
                System.out.println("Response Body:\n" + response.body());
            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        System.out.println("Program ended. Have a great day!");
        scanner.close();
    }
}
```

At the end, as usual, I also showed my little script that increases terminal font size for recording —
and why it didn’t work this time. 😄

Next time, I plan to run both of these mini programs inside Docker containers,
so I can practice Docker Compose and get more comfortable with that workflow.

As always, there were a few minor debugging moments and random Vim chaos along the way.

#### Vim moves

You know what? I’ll start putting the Vim commands I discover during recording at the end of each post.
For example, while writing this one, I was whining that I didn’t know how to jump to the matching bracket.
So I looked it up — and hopefully won’t forget it now.
Also found out how to jump back to the previous cursor position.

```
%   -> jump to matching pair
`'  -> jump back to the previous cursor position 
       (I remapped it to `` because the `' combo is annoying on a Czech keyboard)
```
