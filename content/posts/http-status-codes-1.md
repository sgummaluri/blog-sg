---
title: "Http Status Codes - 1"
date: 2020-05-10T19:56:44+05:30
draft: true,
tags: ["HTTP", "HTTP Status Codes"]
---
It is an undeniable fact that **HTTP** is the de facto standard of the Internet. Wikipedia terms HTTP as an application protocol for distributed, collaborative, hypermedia based information systems. With the advent of the world wide web, came applications that talked to each other over a network to exchange information. Be it the age old RPC style of directly invoking a network service or calling a RESTful endpoint on a server, HTTP is the medium over which this communication happens. Amongst the various use cases of HTTP, web application development has the majority share. 


If we look at the current state of web development, **REST** has gained a huge adoption and has changed the way web applications are built. REST is an architectural style first introduced by *Roy Fielding* in his [PhD dissertation](https://www.ics.uci.edu/~fielding/pubs/dissertation/fielding_dissertation.pdf). Interestingly, Roy Fielding was also a member of the team that drafted the initial version of HTTP. I suggest you go through what REST is all about as that discussion deserves a separate series of articles of its own.


*REST*, traditionally in itself, is heavily reliant on *HTTP* as a protocol for transfer of information between the client (the browser) and the server. The usability of any application is directly proportional to how effectively it handles the scenarios in which the application is faulted. It ultimately boils down to how the developer of the RESTful services builds responses being sent back to the client in general and the errors in specific. Since RESTful services, in most cases, are HTTP services, sending correct HTTP Status codes back to the client becomes very important. Hence it serves a clear purpose to understand the nature of HTTP status codes. Initially, when I started out with writing this series of articles, I did not intend to write them as a series of articles. But midway through writing, it made sense to break the article into a series of articles to be able to discuss clearly the details of the status codes. This series of articles aims to provide enough understanding required to choose the appropriate status code for a scenario be it as simple as returning a HTTP status code to an API call from a browser or when building a public facing API for other devs or end users to build on top of.

## Pretext
This series of posts heavily relies on [RFC 7231](https://tools.ietf.org/html/rfc7231), [RFC 7232](https://tools.ietf.org/html/rfc7232), [RFC 7233](https://tools.ietf.org/html/rfc7233), [RFC 7234](https://tools.ietf.org/html/rfc7234), [RFC 7235](https://tools.ietf.org/html/rfc7235) that layout the HTTP Specification, Authentication, Caching and other semantics. REST, as a paradigm, deals with everything in terms of ‘Resources’. It is important to understand that *Resource* is nothing but a target of a HTTP Request. It could be as simple as a blog post residing on a server and each resource is identified by its *Uniform Resource Identifier (URI)*. It should also be noted that HTTP protocol is a simple specification and *does not* impose any restrictions on any implementations on a User Agent’s side. This post also assumes that the reader has a basic to decent understanding of what REST is all about and that the reader has hand-on experience with implementing RESTful services.

## Categories of HTTP Status Codes
According to [RFC 7321](https://tools.ietf.org/html/rfc7231), all HTTP status codes can be classified into the following five categories.

* Informational (1XX) - Request is received and continuing with processing
* Successful (2XX) - Request is successfully received, understood and accepted by the server.
* Redirection (3XX) - Further action is needed to finish the request
* Client Error (4XX) - There is an issue with the received request (bad request) or can’t be fulfilled
* Server Error (5XX) - The request is valid but server failed to fulfill it.

It should also be understood that HTTP Status codes are extensible as long as they are mapped to the one of the above categories. That means, we can send a custom HTTP Code to the client but only condition is that it should be also of the format (CXX) where C in {1, 2 ,3 ,4 ,5}. The current article is the first of the series and talks about the **Informational HTTP Error Codes**.

### 100 Continue
As developers, we do not get to frequently use HTTP Status Code ```100 Continue```. This is used in scenarios where we want to make sure the server is ready to accept the request. The client sends an ```EXPECT``` value in the request header specifying that it is expecting a ```100-Continue``` from the server. Once it receives the ```100 Continue Response``` from the server, the client continues to send the request. Let’s take a look at the request below (adapted from [RFC 7231](https://tools.ietf.org/html/rfc7231))

Client Request
```
PUT /remote/large-file HTTP/1.1
Host: api.remotesite.com
Content-Type: video/h264
Content-Length: 999999999999
Expect: 100-continue
```

The key thing to notice here is the ```Expect``` header. It is not case-sensitive. This request means that the client is expecting a ```100-Continue``` response from the server and informs the server that it is about to send, maybe a really large request payload and it is expecting an intermediate response of ```100``` before continuing with sending the actual payload. This is very useful in cases where the client assumes that an error or failure is likely and avoids having to send the payload unncessarily in case it doesn’t receive the intermediate response it is expecting.

### 101 Switching Protocols
The ```101 Switching Protocols``` status code is received by a client only when it requests for a change in the protocol used for communication. This is done via the ```UPGRADE``` header field. The server’s ```101``` response also contains an ```UPGRADE``` header field indicating which protocol the communication is being switched to. Also, [RFC 7231](https://tools.ietf.org/html/rfc7231) assumes that the server agrees to switch the application protocol only if there is an inherent advantage in doing so. 

The scenarios where this could be used is the cases involving switching to a higher version of HTTP protocol whenever possible as the higher version might have additional advantages than the lower version (From ```HTTP/1.1``` to ```HTTP/2```). One other scenario may be moving to a real-time protocol (like Websockets) to satisfy the application’s needs (eg. chat applications).

Let us take a look at the handshake that happens when establishing a connection between a client and a server using WebSockets. This is discussed in the [RFC 6455](https://tools.ietf.org/html/rfc6455#section-1.2) that outlines the WebSocket protocol specification.

Client Request
```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

Server Response
```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat
```

The first part of the above snippet denotes the request and the second part denotes the response. Key things to note here are the ```UPGRADE``` header fields that are part of the request payload and response payload. The client is requesting for a switch in the application protocol from ```HTTP``` (as the ```GET``` request signifies) to ```WebSocket``` (denoted by the ```UPGRADE``` Header field). The server responds with the ```101 Switching Protocols``` response and also carries the ```UPGRADE``` header field denoting the protocol it is switching to (Websocket in this case).

### 102 Processing
This status code is used very rarely and is obsolete in the updated versions of the HTTP Specification. In fact, [RFC 7231](https://tools.ietf.org/html/rfc7231) does not make a mention of this HTTP Status code. Hence, we are not going to get into the specifics of ```102 Processing```. It is enough if we understand that this is used in the cases of WebDAV applications. You can read more about it [here](https://evertpot.com/http/102-processing) and [here](https://developer.mozilla.org/en-US/docs/Glossary/WebDAV).

### 103 Early Hints
This is an experimental HTTP Status code that was introduced as a part of [RFC 8297](https://tools.ietf.org/html/rfc8297). This Status code was introduced with the aim to accommodate for optimisations such as ‘preloading’ and reduce the perceived latency on a web page. In a nutshell, this status code allows the server to send some headers early even before the actual response. This is supposed to be used together with the ```Link``` header field that lets the browser preload the resources even before it receives the actual response.

Let’s take a look at the example from the RFC and dissect it. 

Client request

```
GET / HTTP/1.1
Host: example.com
```

Server response
```
HTTP/1.1 103 Early Hints
Link: </style.css>; rel=preload; as=style
Link: </script.js>; rel=preload; as=script

HTTP/1.1 200 OK
Date: Fri, 26 May 2017 10:02:11 GMT
Content-Length: 1234
Content-Type: text/html; charset=utf-8
Link: </style.css>; rel=preload; as=style
Link: </script.js>; rel=preload; as=script
```

In the above example, the client sent a request to the server and received a ```103 Early Hints``` response. Also note the ```200 OK``` that it later received. As a part of the ```103``` that the client received, there are a couple of ```LINK``` header fields. This is a  hint to the client that while the server prepares to send the actual response, it can proceed with loading the resources that have been mentioned in the ```LINK``` header field of the response. So, starting to preload the JS and CSS resources is a good reduction in the total page load time.

However, the caveats here are that the actual response *may* or *may not* include the resources that are mentioned in the intermediate ```103``` response. Hence, the client in this case is not supposed to perform any actions that are not reversible. In other words, the operations that are performed by the client based on an ```103 Early Hints``` response should be *Idempotent* and *Safe* (like a ```/GET``` operation). 
This is all with respect to the Informational HTTP Status codes. We shall see the Successful HTTP Status codes in the upcoming article. 

## Further Reading

* [RFC 7230](https://tools.ietf.org/html/rfc7230#section-6.7)
* [RFC 7231](https://tools.ietf.org/html/rfc7231)
* [RFC 7232](https://tools.ietf.org/html/rfc7232)
* [RFC 7233](https://tools.ietf.org/html/rfc7233)
* [RFC 7234](https://tools.ietf.org/html/rfc7234)
* [RFC 7235](https://tools.ietf.org/html/rfc7235)
* [103 Early Hints discussion on Hacker News](https://news.ycombinator.com/item?id=15590049)
* [102 Processing](https://evertpot.com/http/102-processing)
* [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status#Information_responses)
