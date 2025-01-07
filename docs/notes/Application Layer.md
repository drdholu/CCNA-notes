## Principles

- Core principle of network dev is writing application that run on different end systems and communicate to each other over the network
- Example
	- Web application -> browser program running on the host's end and the web server program running in the web server host.
- You need to write a program that runs on both end systems

## Network Application Architectures

### Client server
- Always on host - **server** which services requests from other hosts - **clients**
- Clients don't interact with each other, they interact directly to the server
- For larger scale applications w/ large clients, big & maintained servers are used - **data centers**.
- Servers have a fixed address and are always on, so clients can contact the server by sending packets to the address at all times.

### P2P

- No contact with any dedicated servers
- Clients are interconnected with each other and directly interact with each other
- Clients are controlled by the users themselves
- P2P is self-scalable
- For example, in a P2P file-sharing application, although each peer generates workload by requesting files, each peer also adds service capacity to the system by distributing files to other peers.

![[Pasted image 20240920232159.png]]
## Processes

### Client Server Process
- In the context of a communication session between a pair of processes, the process that initiates the communication (that is, initially contacts the other process at the beginning of the session) is labeled as the **client**. The process that waits to be contacted to begin the session is the **server**.

### Interface between Process and computer network

- Any message sent from one process to another must go through the underlying network
- Messages are sent to and fro through a software interface called **sockets**
- Socket is the interface between application layer and transport layer, commonly also called as **Application Programming Interface**.
- Application developer has full control over the application layer side of the socket but little control of the transport layer side of the socket.

### Addressing Processes

- In order for a process to send message to another process on another host, the receiving process needs to have an address.
- To identify receiving process, two things are needed
	1. Address of the host (IP address)
	2. Identifier that specified the receiving process in the destination host. (destination port number).

![Pasted image 20240821102009.png](./images/Pasted%20image%2020240821102009.png)

## Transport services (avl to applications)

- Reliable data transfer
	- To support application there must be a protocol that can ensure data sent from one client is sent and received completely by the client on the other end.
	- When a transport-layer protocol doesn’t provide reliable data transfer, some of the data sent by the sending process may never arrive at the receiving process. This may be acceptable for **loss-tolerant applications**
- Throughput
	- rate at which senders sends the bits to the receivers side
	- networks have multiple devices in a bandwidth, making the throughput fluctuate.
	- a service provided by the transport layer is to guarantee throughput of **r bits/sec** upon request of the host.
	- if the throughput requirement is not met, the hosts may retry or giveup. applications with througput requirements are **bandwidth sensitive applications** (ex: multimedia apps)
	- on the other hand, **elastic applications**, can adjust the to the amount of available bandwidith. (ex: email, file transfer, web transfers).
- Timing
- Security

## Transport services (provided by internet)

- TCP
	- Congestion control mechanism
	- Connection Oriented
	- Reliable data transfer
- Enhanced TCP with SSL
	- end-to-end encryption
	- enhancements are made in application layer
	- ssl has its own socket API
- UDP
	- Connectionless oriented
	- No congestion protocol
	- lightweight and minimal

## Application layer protocols

Application layer protocols define how an applications processes run on different end systems.

## Web & HTTP

### HTTP Overview
- **HTTP (HyperText Transfer Protocol)** is a protocol used for transferring data over the web. It defines how messages are formatted and transmitted.
- Two implementations of HTTP, **server and client**
- **Web server** -> server and **web browsers** -> client 
- First TCP connection b/w client and server is established and then communication is done through their socket interface
- HTTP doesn't need to worry about the data being lost or anything, TCP handles it
- HTTP is a stateless protocol

### Persistent & Non Persistent

- Persistent - All req and res sent in one TCP connection
- Non Persistent - New connection made for every req and res

### HTTP connections

> **Round trip time** - Time taken for packet to travel from client to server and back to client. RTT includes packet-propagation delays, packet queuing delays in intermediate routers and switches, and packet-processing delays.

#### HTTP w/ non-persistent connections

- 1 RTT - Send TCP initiation request sent, server ack sent
- 1 RTT - HTTP req w/ client ack sent, html file sent
- New connection for each requested object.
- TCP buffers and variables must be kept in both client and server (to maintain TCP state), this can be a burden to both sides.
- ![[Pasted image 20240915190330.png]]

#### HTTP w/ persistent connection

- 1 RTT - Req sent, HTML sent back.
- TCP connection is made and left on until there isn't any communication/req for a while (this can be configured)
- multiple Web pages residing on the same server can be sent from the server to the same client over a single persistent TCP connection. These requests for objects can be made back-to-back, without waiting for replies to pending requests (pipelining)
- Recent HTTP 1.1 allows multiple server requests to be interleaved in the same connection. It has a priority mechanism which selects which request to respond to.
- ![[Pasted image 20240915190249.png]]
- ![[Pasted image 20240915190401.png]] 

> RTT = 2 * propagation time
> Total = 2RTT + transmit time

### HTTP Message format

There's two types of messages, requests and responses

![[Pasted image 20240921003952.png]]
#### Request Message

```bash
GET /somedir/index.html HTTP/1.1  #request line
# following lines are header lines
Host: www.somedir.com # specifies where the object recides
Connection: close # close the tcp connection after sending this request
User-agent: Mozilla/5.0
Accept-language: fr
```

`HEAD` method is almost similar to `GET` but it only responds with an HTTP msg but leaves out the requested object.

#### Response Message

```bash
HTTP/1.1 200 OK
Connection: close
Data: ...
Server: ...
Last-Modified: ...
Content-Length: ...
Content-Type: ...
```


### Cookies

HTTP servers are stateless, cookies help provide some sort of state to HTTP servers. Cookies help us keep track of user information.

Four comp. of the cookie tech
1. Cookie header line in HTTP req msg
2. Cookie header line in HTTP res msg
3. Cookie file stored in users end system and managed by the browser
4. Backend database at the website.

Coookies create a user session layer on top of the stateless HTTP. When visiting a site for the first time, the server will respond with a msg to browser with a msg,

`Set Cookie: 1679`

When revisiting, the browser sends requests with the cookie file,

`Cookie: 1679`

![[Pasted image 20240824102952.png]]

### Web Caching

- aka **Proxy server**
- satisfies the webs http req on behalf of the web server
- has its own disk storage space & keeps copies of recently requested objects
- both client & server at the same time
- Usually purchased and installed by the ISP's
- ISP's purchase and install these servers in their network and configure their browsers to be directed to the proxy servers.

Pros of web caching
1. Reduces traffic on main web server
2. Reduce response time
3. Improve performance of applications

![[Pasted image 20240824145556.png]]

#### Content Distribution Networks

Is another form of web caching where the ISP needs to only purchase a specific domain and need not worry about setting up an actual server.

A CDN company installs many caches throughout the internet. there are **shared CDNS** & **dedicated CDNS**.


### Conditional GET

This is another form of an HTTP request which is required to check whether the existing object in the web cache is of the latest version or not.

Once the web cache is stored, and it's accessed some time later, the web cache server will send a request to the web server as such

```bash
GET /fruit/kiwi.gif HTTP/1.1 
Host: www.exotiquecuisine.com 
If-modified-since: Wed, 9 Sep 2015 09:23:24
```

The `If-modified-since` is compared with the `Last-modified` header to come across any updates to the object.

If it's not modified, the server will send this back to the proxy server

```bash
HTTP/1.1 304 Not Modified 
Date: Sat, 10 Oct 2015 15:39:29 
Server: Apache/1.3.0 (Unix)
```

And if the object is modified,

```bash
HTTP/1.1 200 OK 
Date: Sat, 10 Oct 2015 15:39:29 
Server: Apache/1.3.0 (Unix) 
Last-Modified: Sat, 10 Oct 2015 10:15:00 
Content-Type: image/gif 
Content-Length: 1024
```


## Email and the internet

Terminologies
- User agents - Sends the mail to the mail server
- Mail Servers - Stores the mail in its Queue and sends the mail to the other mail servers when needed.
- Simple Mail Transfer Protocol (SMTP) - Application Layer protocol which is supported by TCP for reliable data transfer

Flow of mail
```
(SMTP Client) Senders user agent -> Senders Mail server -> (SMTP Server) Recipient's Mail Server -> Recipients Mail Box
```

![[Pasted image 20240825120924.png]]

### SMTP

Facts
- Much older than HTTP
- Data is sent to & fro in 7 bit ASCII format. While sending it's encoded by client and while receiving its decoded by server.
- SMTP first initializes TCP connection with other mail server.
- No intermediate mail servers are used for long distances.

Process
- Client SMTP has TCP establish a connection (**port 25**) with the Server SMTP
- Client and server perform three-way handshaking where the client also mentions the senders and recipients email address.
- The reliable data transfer is left off to TCP.
- Process is repeated if there are more messages over the same connection or the connection is closed.

![[Pasted image 20240825120936.png]]

Example communication

```bash
S:  220 hamburger.edu 
C:  HELO crepes.fr # HELO, MAIL, RCPT TO, DATA, QUIT are part of the dialogue
S:  250 Hello crepes.fr, pleased to meet you 
C:  MAIL FROM: <alice@crepes.fr>
S:  250 alice@crepes.fr ... Sender ok 
C:  RCPT TO: <bob@hamburger.edu>
S:  250 bob@hamburger.edu ... Recipient ok 
C:  DATA 
S:  354 Enter mail, end with ”.” on a line by itself 
C:  Do you like ketchup? 
C:  How about pickles? 
C:  . # end of message, in ASCII -> CRLF (carriage return & line feed)
S:  250 Message accepted for delivery 
C:  QUIT 
S:  221 hamburger.edu closing connection
```

### Comparison w/ HTTP
| HTTP                                                                                       | SMTP                                                                        |
| ------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| **Pull protocol**, where the users use TCP connection to **pull** the info from the server | **Push Protocol**, where the sender initiates a TCP connection to send data |
| Doesn't need any modification to the data being transferred                                | Data transferred must be encoded/decoded in 7-Bit ASCII format              |
| Encapsulates each object in its own HTTP msg                                               | Places all message's objects in one object                                  |
### Mail Access formats

Flow
- Senders user agent sends the msg to its mail server. (using smtp)
- Senders mail server sends msg to recipients mail server. (using smtp)
- Since SMTP is a **push** protocol, the recipients user agent doesn't have any way to get the msg from its mail server.
- Mail access protocol helps user agents **pull** the msg from their mail server.

Mail access protocols
- IMAP
- POP3
- HTTP

1. POP3
	- POP3 begins when the client makes a TCP connection to the mail server on **port 110**.
	- Three phases in POP3
		1. Authorization - user is authorized 
		2. Transaction - user agent retrieves messages, mark msg for deletion, remove deletion marks and obtain mail statistics
		3. Update - occurs after `quit` command is run
	- Server responds with `+OK` & `-ERR`
	- **Authorization** has two principle commands, `user<username>` & `pass<password>` 
	- **Transaction phase** has two methods which can be configured by the user, "download and delete" & "download and keep".
		- *download and delete* mode allows the user to use the `list, retr, dele` commands
		- in *download and keep* mode the user agent leaves the messages on the mail server after downloading them.
	- After the user issues wtv commands they want, the user runs `quit` and POP3 server enters the **update phase**
	- This mode isn't really useful if the user is a nomad who wants to check their mails on different servers over different laptops.
	- POP3 does not carry state information over sessions, it only keeps state information (i.e which mails have been marked for deletion) within a session.
2. IMAP
	- With POP3 one can download the mails on their local machine and maintain different folder for the mails. But this paradigm doesn't allow the user to use these mails on different machines.
	- IMAP is a mail access protocol that allows the user to move mails into different folder, create new folder and move msg's from one folder to other.
	- IMAP maintains state information across sessions
	- Allows user to access/download only a part of a message in low bandwidth connection areas.

> In web-based emails, **the servers and agents now connect over HTTP**. The user agent pulls emails from the mail server using HTTP rather than POP3 and IMAP. When the user wants to send an email, the user agent sends it to the mail server using HTTP.
> However the **mail servers still send and receive messages through SMTP**.

| Feature                          | POP3                                                      | IMAP                                                      |
|----------------------------------|-----------------------------------------------------------|-----------------------------------------------------------|
| **Email Storage**                | Downloads emails to local device; deletes from server.   | Emails remain on the server; only synchronized.           |
| **Device Access**                | Accessed from one device at a time.                       | Accessible from multiple devices simultaneously.           |
| **Email Organization**           | Limited organization; cannot create folders on the server.| Allows creation and management of folders on the server.  |
| **Synchronization**              | No synchronization; changes not reflected across devices. | Synchronizes changes across all devices (e.g., read/unread status). |
| **Offline Access**               | Emails can be read offline after download.                | Requires internet connection to access emails fully.      |
| **Connection Ports**             | Port 110 (unencrypted), 995 (encrypted).                 | Port 143 (unencrypted), 993 (encrypted).                 |
| **Use Case**                     | Suitable for single-device use, limited internet access.  | Ideal for users needing access from multiple devices.     |
| **Server Load**                  | Lower server load due to local storage of emails.         | Higher server load due to email storage and synchronization. |
| **Backup and Recovery**          | Emails lost if deleted after download unless kept on server.| Multiple copies stored, allowing recovery if local data is lost. |
| **Complexity**                   | Simpler and easier to implement.                          | More complex, requiring more resources and management.    |


## DNS Servers

- Two ways to identify a host, **hostname** & **ip address**. 
- DNS servers help in resolving the hostnames into ip addresses so that routers/switches can operate.
- Operates on **port 53** and runs over **UDP**
- Deployed by application layer protocols (HTTP & SMTP) to resolve hostnames.
- Adds additional delay, but sometimes IP addresses are cached in nearby servers.

DNS is a 
1. Distributed database implemented in a hierarchy of DNS servers
2. Allows hosts to query the distributed database.

Features
1. Host aliasing
	- One hostname (canonical name) -> multiple aliases
	- DNS is invoked by application to obtain cname for a supplied alias
2. Mail server aliasing
	- Similar to host aliasing, DNS is invoked by mail application to obtain cname for a supplied alias.
3. Load distribution
	- Busy servers are often replicated over many servers to offload heavy traffic over to other servers. Each server runs on different end systems having different IP addresses.
	- Client makes a query for a name which is mapped to several addresses, the server returns a set of addresses. 
	- The server follows a mechanism where for each query it rotates the set as the client will typically pick the top address in the set.

> Web server and Mail server can have the same canonical name


### Overview of DNS working

- Application invokes the DNS server to resolve a host name.
- DNS takes over and sends a request to the network
- All requests & messages are done over UDP on **port 53**
- DNS server sends a reply to DNS in host side with the required mapping. 
- Mapping is passed to the invoked application

All the DNS mapping arent on one server.
- Maintenance
- Traffic/Load on one server
- Single point of failure
- Distant database of mappings

### Distributed Hierarchy System

Since one server can't handle/have all the mappings present, there are multiple servers scattered around the world. The servers are designed in an **hierarchical** manner.

1. Root DNS Servers
	- About 400 servers
	- Provides with the IP addr of the tld server
1. Top Level Domain Servers
	- Each tld has it's own tld server
	- Provides with the IP addr of the auth server
1. Authoritative Servers
	- Each organization with a public host name has to have its IP address publicly listed.
	- Orgs can have their own servers or pay money to a service provider for the same.

> **Local DNS Server**: Doesn't belong to the hierarchical system but is **central** to the DNS structure. ISP's set up their own local DNS servers. The addresses of these local servers is sent to the host when a host connects to the ISP. When a host makes a DNS query, the query is sent to the local DNS server, which acts a proxy, forwarding the query into the DNS server hierarchy.

#### Example

`cse.nyu.edu` wants IP addr of -> `gaia.cs.umass.edu.`

What we know,
- Local DNS for `cse.nyu.edu` -> `dns.nyu.edu`
- Auth DNS for `gaia.cs.umass.edu` -> `dns.umass.edu`

Flow =>
```bash
[Host: cse.nyu.edu]
     |
     | (1. Send DNS Query for "gaia.cs.umass.edu" to Local DNS Server)
     v
[Local DNS Server: dns.nyu.edu]
     |
     | (2. Forward Query to Root DNS Server)
     v
[Root DNS Server]
     |
     | (3. Respond with IP Addresses of TLD Servers for "edu")
     v
[Local DNS Server: dns.nyu.edu]
     |
     | (4. Forward Query to TLD DNS Server for "edu")
     v
[TLD DNS Server for "edu"]
     |
     | (5. Respond with IP Address of Authoritative DNS Server for "umass.edu")
     v
[Local DNS Server: dns.nyu.edu]
     |
     | (6. Forward Query to Authoritative DNS Server: dns.umass.edu)
     v
[Authoritative DNS Server: dns.umass.edu]
     |
     | (7. Respond with IP Address of "gaia.cs.umass.edu")
     v
[Local DNS Server: dns.nyu.edu]
     |
     | (8. Send IP Address of "gaia.cs.umass.edu" back to Host: cse.nyu.edu)
     v
[Host: cse.nyu.edu]
     |
     | (9. Use IP Address to connect to "gaia.cs.umass.edu")
     v
[Establish Connection with "gaia.cs.umass.edu"]

```

The above requests are both **iterative** & **recursive**.

- Iterative -> All except 1
- Recursive -> 1(since cse makes a request to dns for host & dns resolves it all on its server)

![[Pasted image 20240826201041.png]]

Here’s a comparison of iterative and recursive DNS requests in table form:

| Feature                          | Iterative DNS Requests                                   | Recursive DNS Requests                                   |
|----------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| **Client Involvement**           | High; client queries multiple servers sequentially.     | Low; client delegates all queries to the DNS server.    |
| **Caching**                      | Primarily at the resolver/server level; limited client-side caching. | Local caching at the DNS resolver to speed up future requests. |
| **Process Flow**                 | Client receives referrals from servers and queries each one until a response is obtained. | Resolver queries other servers on behalf of the client until a complete answer is found. |
| **Resolving Server Behavior**    | Server provides referrals to other servers without fetching the complete answer. | Server performs all necessary queries and returns the complete answer or an error. |
| **Efficiency**                   | Can be less efficient due to multiple queries by the client. | More efficient for the client as it requires fewer actions on their part. |
| **Use Case**                     | Useful in scenarios where clients need to manage their own queries or when using lightweight resolvers. | Commonly used in most DNS lookups, especially in user-facing applications where ease of use is essential. |

### DNS caching

DNS caching plays a big role in DNS resolving. It reduces delay performance and also reduces the traffic in the network.

DNS caching is what allows DNS servers to learn/cache IP addresses. It caches the mapping in its local memory. Since these mappings are never permanent, the saved addresses are discarded after a while.

### DNS records and Messages

#### Records

Servers that implement DNS distributed servers also store **resource records (rr)**. Each DNS reply carries one or more dns records

- Structure: `(Name, Value, Type, TTL)`
	- TTL: time to live of the rr.

1. Type = A
	- Name is the hostname
	- Value is the ip address of the host name
	- Standard hostname to IP addr mapping
2. Type = NS
	- Name is domain
	- Value is the hostname of auth dns server
	- Used to route dns queries further down the chain
3. Type = CNAME
	- Name is the alias domain
	- Value is the IP addr of the canonical name
4. Type = MX
	- Name is the email alias domain
	- Value is the IP addr of the canonical email name

> Servers can have the same alias for web servers and mail servers. To obtain either we use the specific type as mentioned above.


#### Messages

The query and reply sent by a DNS server follow a similar pattern

1. Header Section (12 bytes)
	- 16 bit identifier identifies the query
	- Flags such as, 1 bit query(0)/reply(1), 1 bit auth flag, 1 bit recursion-desired flag, 1 bit recursion-available flag
2. Question section (variable)
	- **Name** field which contains the name of the thing that is being queried.
	- **Type** field which tells about the type of the question being asked about the name (A, MX, etc.).
3. Answer section (Variable)
	- Contains the resource records for the name that was queried
	- May contain multiple rr's
4. Authority section (variable)
	- Contains records of authoritative servers
5. Additional section (variable)
	- Additional info such as CNAME's of email/hostname aliases.

![[Pasted image 20240826223758.png]]

To illustrate the structure of a DNS query and response, consider a request to resolve the domain name `example.com`. Table below shows the interpretation of the bytes of the request. (Note that the exact structure of the UDP datagram consists of just the bytes shown, concatenated in order: `123401000001...`) The header starts with a **16-bit randomly chosen identifier** denoted as *XID* (`1234` in our example), followed by a 16-bit value that serves as *a bit mask*. The structure of the bit mask is shown in [Table 4.8](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/UDPSockets.html#tbl4-8). The rest of the header after the bit-mask indicates how many entries are in each of the other fields, each as a 16-bit value.

Table 4.7
![[Pasted image 20240828085815.png]]

[Table 4.8](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/UDPSockets.html#tbl4-8) illustrates the structure of the **16-bit flag field** that *follows the XID field of a DNS header*. In the message shown in [Table 4.7](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/UDPSockets.html#tbl4-7), the only bit set is the `Recursion Desired` (`RD`) bit. The `Opcode` field indicates that this is a standard query (`SQUERY = 0000`). In the response shown in [Table 4.9](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/UDPSockets.html#tbl4-9), the flag value is `0x8180`, which means that the Query Response (`QR`) bit has been set to indicate the message is a response, as well as the `Recursion Available` (`RA`) bit. **The `RCODE` field is used to indicate if an error occurs**, and all 0 bits there indicates there was no error processing the query. Information on the other fields is available in RFC 1035.

![[Pasted image 20240828085910.png]]

[Table 4.9](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/UDPSockets.html#tbl4-9) shows the response for the query from [Table 4.7](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/UDPSockets.html#tbl4-7). In the response, the `header` is almost identical to that of the request. *The randomly chosen identifier `XID` should match the original request*; if the resolver has sent multiple requests, the `XID` field allows the resolver to determine which request is being answered. *The bit mask has been modified to denote that this message is a response* and the recursive resolution strategy is available. The `header` also indicates that *a single answer has been provided*. The `question` field is identical to the original request.

![[Pasted image 20240828090348.png]]

#### Inserting records in DNS DB

1. Register your domain through the **registrar** (requires a small fee to be paid). Registrar ensure uniqueness.
2. Provide **IP addresses** of 1st, 2nd, (so on) **auth DNS servers**. For each server there would be a Type A and Type NS record in the TLD servers.
3. Make sure to have Type MX and A rr's entered in your auth DNS server.

> [4.6. UDP Socket Programming: DNS — Computer Systems Fundamentals (jmu.edu)](https://w3.cs.jmu.edu/kirkpams/OpenCSF/Books/csf/html/UDPSockets.html)

## Socket Programming