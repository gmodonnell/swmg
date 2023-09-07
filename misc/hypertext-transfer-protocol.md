# HyperText Transfer Protocol

HTTP/S is a Layer 5 Protocol. It is the most prominent Layer 5 protocol in terms of volume. SMTP and FTP are also Layer 5 protocols. Things to consider about HTTP:

> * HTTP is used to transfer HTML and its associated code (JS, CSS, etc.)
> * HTTP is text-based, which means it's human readable if you intercept it.
> * HTTP is based on TCP.
> * HTTP uses TCP 80 as a "default port". HTTPS uses 443.

HTTP/S files are structured text files served from a node containing logical links to other nodes containing more structured text files. HTTP handles most of our web traffic, and is the protocol we rely on to deliver HyperText Markup Language (HTML) to clients. HTTP can also deliver files of most any type. Multiple connections can be opened on the same device if you want higher throughput or a variety of resources at once. Truly a great protocol. Tim Berners-Lee has a net worth of $10 million. Humanity has strayed from God's light.

## URLs
Format of URLs:
![URL Structure, HTB Academy](https://academy.hackthebox.com/storage/modules/35/url_structure.png)

Web requests need a DNS server to translate URLs to IP addresses, where the requests are routed and from which the content is served. DNS servers are not required if a manual entry is added in ```/etc/hosts```. This file exists on Linux and Windows. On Windows it is located at ```%SystemRoot%\System32\drivers\etc\hosts```.

## Response and Request Types

There are a number of response and request types used by HTTP to make the client/server negotiation run smoothly. Request types are issued from the client to the webserver.

> ```
>     GET - requests a resource from the webserver
>    POST - sends user input, like form data or chats
>    HEAD - retreives the header information only
>     PUT - upload client file to server. Internet FTP!
>   TRACE - acks to client
> OPTIONS - shows the HTTP methods supported by the server
> CONNECT - pushes the connection upward to a secure environment
>  DELETE - deletes an existing resource on the webserver
>   PATCH - passes updates, rarely seen
> ```

Response codes are returned based on what the server is able to do with your request. They are categorized numerically.

> ```
> 100 - Informational Status Code. (100 Working on Request)
> 200 - OK. Anything 2xx is good! You have succeeded.
> 300 - Handles Types of Redirections. (301 Moved Permanently)
> 400 - Contains Errors (404 Not Found, 403 Unauthorized)
> 500 - Internal Server Errors (500 Internal, 501 Not Implemented)
> ```

400 Errors are Client Side errors, while 500 Errors are Server Side.

Requests can be manually constructed provided you know the 3 main parts of it: Method, Path and Version:
```GET /users/login.html HTTP/1.1```
The Method is the Request Type, the Path is the content you want served and the Version is the HTTP version you want to use to serve the content. HTTP 1.X sends requests as clear-text and uses a new-line for each header while HTTP 2.X sends requests as binary data in dictionary form. Responses will contain the Version and Response Code, then headers and finally the response body containing the content you want:
```HTTP/1.1 200 OK```
Using ```cURL``` with the ```-v``` verbosity flag will let you see the full HTTP request and response when you send and receive them. Very fun trick.

## Headers {.tabset}

### General Categories
Headers can be split into five categories:
* General Headers
* Request Headers
* Response Headers
* Entity Headers
* Security Headers

### General Headers
Used in both the request and response, General headers describe the message. Two common general headers are the ```Date``` header, which has a timestamp of the request, and the ```Connection``` Header that determines if the current network connection should stay active after the request resolves. ```close``` and ```keep-alive``` are the two most common values of this header.  

### Request Headers
Sent by the client, these headers do not relate to the content either. 
| Header | Example | Description |
| ------ | ------ | ------ |
| Host | ```Host: www.onemorelevel.com``` | Specifies the Host being queried for the resource. Can be a Domain or IP. Can sometimes give away the existence of other hosts on the target server. |
| User-Agent | ```User-Agent: curl/8.0.1``` | Describes the Client reuqesting the resources. Often needs to be changed to avoid filters. | 
| Referer | ```Referer: http://www.google.com``` | Describes where the current request is originating. Clicking a link on a search engine makes that search engine the referer. This header can be easily manipulated. |
| Accept | ```Accept: */*``` | Describes which media types the client can interpret. Can be CSV or wildcard. |
| Cookie | ```Cookie: PHPSESSID=42069``` | Key-Value pairs that are passed to the server to gain or maintain access to data. | 
| Authorization | ```Authorization: BASIC asdfghjkl``` | Alternative method of Client identification. After successful auth, the server returns a token to the client that is stored here. Stored by the client and retrieved by the server, which is why they can be hijacked. | 

### Response Headers
Sent by the server, these headers do not related to the content but provide context about the response itself. 
| Header | Example | Description |
| ------ | ------ | ------ |
| Server | ```Server: Apache/4.2.069``` | Discloses server version and architecture. Shouldn't be publicly enabled. |
| Set-Cookie | ```Set-Cookie: PHPSESSID=asdfghjkl``` | Tells the client to store a particular cookie value. | 
| WWW-Authenticate | ```WWW-Authenticate: BASIC realm="localhost"``` | Tells the client the type of authentication required to access the requested resource. |

### Entity Headers
Commonly used in both the Request and Response. These headers describe the content and are generally found in POST or PUT requests. 
| Header | Example | Description |
| ------ | ------ | ------ |
| Content-Type | ```Content-Type: text/html``` | Describes the type of resource being transferred. A ```charset``` field can be included to denote an encoding standard. |
| Media-Type | ```Media-Type: application/pdf``` | Describes the type of data being transferred. The ```charset``` field can be included in this header as well. |
| Boundary | ```boundary="asdfghjkl"``` | Provides a marker to separate content, such as parts of form data. |
| Content-Length | ```Content-Length: 385``` | Holds the size of the entity being transferred. Needed by server to read data from the message body. Automatically generated by the browser. |
| Content-Encoding | ```Content-Encoding: gzip``` | The type of encoding used on the data being transferred/ |

### Security Headers
| Header | Example | Description |
| ------ | ------ | ------ |
| Content-Security-Policy | ```Content-Security-Policy: script-src 'self'``` | Dictates the website's policy towards externally injected resources. Tells the browser to accept resrouces from certain trusted domains, helping prevent XSS and other related attacks. |
| Strict-Transport-Security | ```Strict-Transport-Security: max-age=42069000``` | Forces HTTPS and prevents attackers from sniffing traffic. |
| Referrer-Policy | ```Referrer-Policy: origin``` | Helps avoid disclosing sensitive URLs by determining if the ```Referer``` header should be displayed or not. |

These example headers are only a subset of all the headers in the game. 
