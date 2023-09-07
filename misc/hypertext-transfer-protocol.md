# HyperText Transfer Protocol

HTTP/S is a Layer 5 Protocol. It is the most prominent Layer 5 protocol in terms of volume. SMTP and FTP are also Layer 5 protocols. Things to consider about HTTP:

> * HTTP is used to transfer HTML and its associated code (JS, CSS, etc.)
> * HTTP is text-based, which means it's human readable if you intercept it.
> * HTTP is based on TCP.
> * HTTP uses TCP 80 as a "default port". HTTPS uses 443.

HTTP/S files are structured text files served from a node containing logical links to other nodes containing more structured text files. HTTP handles most of our web traffic, and is the protocol we rely on to deliver HyperText Markup Language (HTML) to clients. HTTP can also deliver files of most any type. Multiple connections can be opened on the same device if you want higher throughput or a variety of resources at once. Truly a great protocol. Tim Berners-Lee has a net worth of $10 million. Humanity has strayed from God's light.

### URLs
Format of URLs:
![URL Structure, HTB Academy](https://academy.hackthebox.com/storage/modules/35/url_structure.png)

Web requests need a DNS server to translate URLs to IP addresses, where the requests are routed and from which the content is served. DNS servers are not required if a manual entry is added in ```/etc/hosts```. This file exists on Linux and Windows. On Windows it is located at ```%SystemRoot%\System32\drivers\etc\hosts```.

#### Response and Request Types

There are a number of response and request types used by HTTP to make the client/server negotiation run smoothly. Request types are issued from the client to the webserver.

> ```
>     GET - requests a resource from the webserver
>    POST - sends user input, like form data or chats
>    HEAD - retreives the header information only
>     PUT - upload client file to server. Internet FTP!
>   TRACE - acks to client
> OPTIONS - shows the HTTP methods supported by the server
> CONNECT - pushes the connection upward to a secure environment
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
