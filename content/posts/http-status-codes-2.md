---
title: "HTTP Status Codes - 2"
date: 2020-05-25T19:23:14+05:30
draft: false
tags: ["HTTP", "HTTP Status Codes"]
---
This article is the second in the HTTP Status Code Series and deals with the category of Successful Responses. The first article in the series can be found [here](/posts/http-status-codes-1).

We are going to take a look at the most frequently used Status codes for a successful response that is sent from the server to the client. Also, bear in mind that we are not going to deal with the status codes ```207 Multi-Status``` and ```208 Already Reported```, which are used in the WebDAV cases; the ```226 IM Used``` status code which is used with HTTP Delta Encoding. You can read more about HTTP Delta Encoding [here](https://tools.ietf.org/html/rfc3229).

### 200 OK

This is the most widely used status code in the world of RESTful services. This response indicates that a request from the client is accepted by the server. In simpler terms, the request that is pushed to the server has succeeded. This response is cacheable by default, unless we specify otherwise.

Client Request
```
GET /hello.txt HTTP/1.1
Host: www.example.org
Accept: text/*
```

Server Response
``` 
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2009 12:28:53 GMT
Server: Apache
Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
ETag: "34aa387-d-1568eb00"
Accept-Ranges: bytes
Content-Length: 51
```

However, note that the interpretation of the body of the ```200 OK``` response usually depends on the HTTP verb that is used to make the request. Below is the summary of the interpretation of ```200 OK``` with different HTTP verbs


#### GET
The payload for the case of a ```GET``` represents the target resource on the server side. In the above example, it is going to be the representation of the resource hello.txt that we performed a ```GET``` on.

#### HEAD
The representation in the case of ```HEAD``` is the same as that of ```GET```, except that there is no representation of the data that is returned and just contains the headers.

#### POST
When ```200 OK``` is returned with a ```POST``` request, it usually is a representation of the status of the action or the entire resource that is obtained as a result of the action that is triggered through the ```POST```.

#### PUT, DELETE
The response payload for ```PUT``` and ```DELETE``` verbs denotes the representation of the status of the action that is performed.

### 201 Created

This status code signifies that the request from the client is accepted by the server and processed successfully and has created one or more new resources as a part of the request. The payload that is sent along with the ```201 Created``` response usually contain a ```ETag``` header or a ```Location``` header or both. The content of these headers in the payload is for identifying where the new resources are created.

Client Request
```
POST example.com/add-hello HTTP/1.1
Content-Type: application/json

{“name”: “hello”}
```
 
Server Response
```
HTTP/1.1 201 Created
Location: /resources/1001
```

In the above example, we are using the ```POST``` HTTP verb to trigger the request from the client side and the response payload contains the ```Location``` Header. This header field represents the effective URI that can be used to identify the newly created resource.

In the case of the ```PUT``` HTTP verb, there is no ```Location``` header that is sent as a part of the response payload and in this case, the request URI itself can be used to identify the resource that is created as a part of the request that is triggered by the client.

### 202 Accepted

This status code indicates that the server has accepted the request for processing and the actual processing of the request itself is under-way and has not completed yet. The eventual result of the processing could vary and the request processing may not run through completion for whatever reasons. 

The specification [RFC 7231](https://tools.ietf.org/html/rfc7231#page-51) itself says that this status code is ```“Intentionally NonCommital”```. This could be used in scenarios where the request is ought to trigger a batch processing job that is asynchronous in nature. By default, HTTP does not have a way to resend the status of an asynchronous operation. 

The payload of the response ideally contains a status-monitor that is used to provide information to the user as to what the current status of the job that is triggered by the request is.

Client Request
```
POST /long-running-job HTTP/1.1
Content-Type: application/json
Host: example.org
```

Server Response
```
HTTP/1.1 202 Accepted
Link: </running-job-status/> rel="http://example.org/running-job-status"
Content-Length: 0
```

### 203 Non-Authoritative Information

This status code is not generally returned by the server that is handling the requests itself, but by a ```HTTP proxy``` that sits between the client and the server. This response indicates that the proxy sitting in between has actually modified the response body from the server and hence the **“Non-Authoritative Information”**. A downside or a side-effect of this status code is that the client never gets to know what the actual response from the server was and that information is lost forever. 

To get around the loss of information with respect to the actual response, it is suggested to use the ```Warning``` field specified in the [RFC 7234](https://tools.ietf.org/html/rfc7234#section-5.5). As per the specification, a ```Warning``` field is used to add additional information about the modification made to the response message which might not be conveyed as a part of the status code. 

When using a ```Warning``` field, we add a specific *warning code* to the value of the field and that warning code indicates the type of *transformation* that is applied at the proxy level. Also, the status code that is returned by the server is preserved. Hence, there is no loss of information with the use of ```Warning``` field.

Client Request
```
GET /my-resource HTTP/1.1
Host: example.org
```

Server Response (without Warning field)
```
HTTP/1.1 203 Non-Authoritative Information
Content-Type: text/html
```

Server Response (with Warning Field)
```
HTTP/1.1 200 OK
Content-type: text/html
Warning: 214 proxy.example.org “The response HTML has been modified”
```

One thing to note here is the ```Warning``` Code ```214``` that is added to the ```Warning``` header field. ```214``` means *‘Transformation Applied’*. You can learn more about the warning codes [here](https://tools.ietf.org/html/rfc7234#section-5.5.1).

### 204 No Content

This status code is the same as ```200 OK```, but with only difference that ```204 No Content``` is returned in cases where the server has accepted the request successfully and has nothing that is to be sent back to the client. A real world scenario where this can be returned is the scenario where a client need not shift to a different document view after the request it triggered has successfully been accepted and acknowledged. This response is cacheable by default.

Client Request
```
POST /updateView HTTP/1.1
Host: example.org

{“name”:”hello”}
```

Server Response
```
HTTP/1.1 204 No Content
```

### 205 Reset Content

This status code indicates that the server has successfully processed the request and requires the client to reset the document view to the original state as received from the origin server. In the real world, an appropriate use case for this status code could be a data entry form, which when filled and submitted to the server, has to be reset to the original state for the next entry to be made, so that the user can start filling in the next entry.

There will not be any payload generated for this type of response from the server. The server **MUST** do one of the following in the case of ```205 Reset Content```

* Send a ```Content-Length``` header field with value set to 0
* Send a zero length payload by including a ```Transfer-Encoding``` header field with a value of chunked and a message body consisting of single chunk of zero-length
* Close connection immediately after sending blank line terminating header section


To put things in perspective, ```204``` status code can be returned in cases where there are a series of edits on a single document and ```205``` status code can be returned in cases where there are a series of document additions.


### 206 Partial Content

This status code is returned in cases where a client wants to retrieve a part of a really large file. Since not every server supports this, the client must get to know of the availability of the support for partial content on the server side. To let a client know that partial content requests are supported, the server needs to send the client an ```Accept-Ranges``` header (maybe when the client triggers a ```HEAD``` request). Once the client sees the ```Accept-Ranges``` header field in the response payload, it can proceed to issue a request to the server that contains a ```Range``` header field, specifying the bytes range it wishes to receive. Below series of request/response snippets help understand this clearly

Client Request
```
HEAD /getResource HTTP/1.1
Host: example.org
```

Server Response
```
HTTP/1.1 200 OK
…
Accept-Ranges: bytes
Content-Length: 5000
…
```

Client Request
```
GET /getResource HTTP/1.1
Host: example.org
Range: bytes=0-999
```

Server Response
```
HTTP/1.1 206 Partial Content
…
Accept-Ranges: bytes
Content-Range: bytes 0-999/5000
…

{data}
```

The final server response snippet shows the partial content of the first 1000 bytes being sent from the server as a part of the ```206 Partial Content``` response.