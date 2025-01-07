pTransport Layer protocols provide a **logical communication** between application processes running b/w hosts. This logical communication allows processes to send messages to each other.

The TL protocols are implemented on **end systems**. When the process wants to send message to another process, the message will pass through its application layer to its transport layer. Here the transport layer will split this message into chunks and attach a transport layer header, these chunks are called **segments**.

The segments are sent down to the network layer where they are encapsulated with the network layer header. (**datagram**)

Transport layer provides logical communication between processes in end systems whereas network layer provides logical communication b/w hosts.

> Service provided an upper layer usually depends on a service provided by a lower layer.

Transport layer packets are referred to as **segments**. Particularly, packets sent using **TCP** are **segments** and using **UDP** are **datagrams**.

Since **network layer** packets are also **datagrams**, we refer to TCP/UDP packets as segments.

Network layer has its own protocol, **Internet Protocol**. It's called a **best effort delivery service protocol**, i.e. it tries its best to deliver the packets but it doesn't guarantee the delivery of the packets. So we can call the **IP a unreliable protocol**.

The IP is aided with the UDP/TCP to make it more reliable. TL protocols extend their help to aid IP to help with its delivery service.

Although, IP aided with UDP is actually more unreliable compared to IP aided with TCP as TCP provides reliable data transfer and congestion control. 

> Congestion control allows equal share of bandwidth to TCP connections. This protocol prevents one connection from swamping the links & routers 

Extending host-to-host delivery to process-to-process is called **transport layer multiplexing and demultiplexing**.

## Multiplexing & Demultiplexing

The host-to-host delivery service provided by the network layer extended to a process-to-process delivery service for applications running on hosts. This service is required by every computer running applications.

A process can have one or more sockets at the same time, hence the sockets require a unique identifier which is the **port number**. But instead of sending the message directly to the socket at its port number, the msg is first sent to an **intermediate port**.

At the receiving end, the transport layers receives the segments and examines it for the destination port number and sends the message accordingly. This is **demultiplexing**.

The job of gathering data, port numbers (source and destination) & attaching the transport layer header to the segments and then sending this to the network layer is called **multiplexing**
![[Pasted image 20240830100108.png]]

Hence Multiplexing requires,
1. Unique identifier for ports
2. Each segment having special fields indicating the destination socket.

![Pasted image 20240830100228.png](./images/Pasted%20image%2020240830100228.png)

> Port number is 16 bit number ranging from 0 to 65535. Well known ports range from 0 to 1023. 1024 to 49151 are Registered ports. 49152 - 65535 are dynamic/private ports.

### Connectionless mux and demux

- UDP is used this.
- During UDP socket programming, OS automatically assigns ports numbers to sockets or the application programmer can assign it their selves.
- If the programmer is implementing a well known port program, they must assign the socket the well known port itself.
- **UDP socket** is fully identified by a **two tuple**, dest. IP addr and dest. port number
- If two UDP segments have different source IP addr and/or port numbers but **same** dest. ip & port number, then the two segments are directed to the same process via the same socket.

Example

Host A (UDP port 19157) wants to send to -> Host B (UDP port 46428)

- A creates segment which includes => Dest. port, source port, and other values
- segment -> n/w layer -> encapsulated w/ n/w header -> datagram
- A datagram sent to B. 
- B transport layer receives and examines segment.
- Sends message to desired port (46428)

### Connection oriented mux and demux

- **TCP socket** is identified by a **four tuple**, source IP, source Port, dest. IP, dest Port.
- Two TCP segments with different source IP and/or port numbers are directed to two **different** sockets.
- In a simple TCP implementation application, the server side has a "welcome socket" ready to receive connection requests. The TCP client sends a **connection establishment request**.

> Connection establishment request contains nothing but the dest. port number and some special connection establishment bit set in TCP header and source port number.


#### Port security

A process on the server usually waits for requests on an **open port**. Once the client finds this open port it can easily map out the process linked to it. 

This can be useful in finding which network applications are running on the host. But this can also lead to a security vulnerability, if the open port has a known security flaw, then the host is doomed for an attack.

### Web server & TCP

Earlier web servers created a new process which had its own socket for every new TCP connection made. But new high-performing web servers use only **one connection**. 

Servers create threads for each new connection with a new socket for each connection. So there could be at a given moment there could multiple sockets pointing to the same process.

In a persistent connection, one socket is responsible for HTTP communication between client and server.

In non-persistent connection, multiple sockets are created and closed for each request & response between the client and server. This can create load on a busy server.

## Connectionless Transport: UDP

- UDP is a connectionless protocol with little error checking and mux & demux functions.
- UDP adds almost nothing to IP.

We know, 
- UDP takes the message from application process, attaches **source & dest** port number for mux & demuxing and other small fields.
- This segment is encapsulated with the transport layer header and sent to the network layer. Then it's further encapsulated with the network header to form the IP datagram. This datagram is then sent to the destination.
- As we see, there is no handshaking done between the source & destination. Hence UDP is connectionless.

And,
- DNS is another application layer protocol that uses UDP as its underlying protocol.
- The **host side** UDP makes its segment, passes it to the network layer to make the datagram and this is sent to the **name server**
- If DNS application at the host side doesn't get any reply, it tries sending a query again & if after repeated trying it doesn't get a reply, it informs the invoking application.

Why UDP over TCP if it's unreliable?
1. **Finer application layer control over when data is sent**
	- UDP quickly encapsulates the message w/ the headers and port information and sends it to the network layer. 
	- On the other hand TCP has congestion control & does a three-way handshake. TCP keeps on sending a packet until it receives the ack. packet back from destination.
2. **No connection establishment**
	- UDP just blasts away without any formal preliminaries. UDP doesn't introduce any delays.
	- This is probably the reason why DNS using UDP.
	- Google Chrome browsers use QUIC, Quick UDP Internet Connection. Which uses UDP as its underlying transport protocol and implements reliability in an application layer protocol on top of UDP.
3. **No connection state**
	- TCP maintains a connection for each request and response whereas UDP doesn't maintain any connection and doesn't track any parameters
4. **Small packet header overhead**
	- UDP has only 8 bytes of header and TCP has 20 bytes of header.

> But the delays introduced by HTTP over TCP is necessary to download important documents.

![Pasted image 20240901135722.png](./images/Pasted%20image%2020240901135722.png)

- UDP provides no congestion protocol but this protocol needed in a congested state in which very little useful work is done.
- When there is a congested state, all the UDP packets wouldn't traverse from source to destination. This would in turn block the TCP packets.
- Although, reliability is something that can be implemented in the application itself while using UDP as the underlying protocol.
- QUIC is an example of such thing.
### UDP segment structure

- **Application data** contains DNS query or response message or application data itself.
- **UDP header** has four fields, each of 2 bytes.
- **Length field** specifies the number of bytes in the UDP segment.
- **Checksum** field tells whether any error has occurred or not.
![Pasted image 20240901141812.png](./images/Pasted%20image%2020240901141812.png)

### Checksum

- Checksum is a method to check for any errors/if any bits are manipulated in the segment.
- First addition of the 16 bit words in the segment is done, Any overflow encountered is wrapped around.
- After getting the sum, 1's complement on the sum is performed. This resulting 16 bits are sent along with the other 16 bit words.
- The destination receives this segment and adds up these 16 bit words along with the checksum. If the result is just 16 1's, then there is no error. If there is a 0, then there is an error.

But why does UDP need this error detection method?
- No guarantee about error checking between source and destination
- In router memory could destory bits.
- UDP provides error checking on an **end to end** basis
- Although UDP provides this error checking method, it doesn't provide any method to overcome it.

> OS fundamental **end to end** principle: Functions provided at the lower level maybe redundant or of little value when compared to the cost of providing them at the higher level.


## Principles of reliable data transfer

![[Pasted image 20240901164358.png]]

> rdt_send() - reliable data transfer function used by senders, invoked from above.
> udt_send() - unreliable data transfer allows data to be sent to the other side.
> rdt_recv() - called when data is received by receiving side.
> deliver_data - data sent to upper layer

1. First iteration
	- Sender side
		- Get data from upper layer (system call to upper layer)
		- Pack data
		- Send data
	- Receiver side
		- Get data from lower layer (system call to lower layer)
		- Unpack data
		- Send data to upper layer
	- ![[Pasted image 20240901161206.png]]
1. Second iteration (stop and wait ARQ)
	- Sender side
		- After sending expect a **ACK** or **NACK** packet
		- if NACK, send data again
	- Receivers side
		- After getting data, send ACK (if not corrupt) or send NACK (if corrupt)
		- wait for data
	- Retransmission of data is aka **Automation Repeat reQuest** protocol (ARQ)
	- ![[Pasted image 20240901161227.png]]
3. Second but better iteration (adding sequence number)
	- Problems: ACK, NACK could be corrupted
	- Solution: Data packets are included with a **sequence number**. Note that ACK & NACK need not include this number.
	- Senders side
		- Send data but now with a sequence number and wait for ACK, NACK
		- if NACK/ACK not received on time, send data again, else move to next sequence number
		- ![[Pasted image 20240901162937.png]]
	- Receivers Side
		- Get the packet and check if sequence is as expected, send an ACK otherwise also send an ACK
		- If packet is corrupted, send a NACK with the checksum
		- ![[Pasted image 20240901162927.png]]
- Second but slightly better iteration (NACK free)
	- Improvised: Instead of sending NACK, send a ACK for the previous packet, the senders side will understand that the recently sent packet was corrupted.
	- Here, ACK needs to include the sequence number now.
	- Senders Side
		- after sending packet wait for ACK
		- if dup ACK is received, for prev packet, retransmit current packet.
		- ![[Pasted image 20240901163227.png]]
- Third iteration
	- Problem: Underlying channel can lose packets. We need to detect packet loss and what to do when packet loss occurs.
	- Intuition: We need a mechanism where the sender doesn't wait for the worst-case scenario (round trip delay b/w recv and sender + amt of time to process packet at recv) but instead waits for a selected amt of time.
	- Solution: Since sender doesn't know whether packet/ack was lost or overdelayed, the sender simply retransmits the packet. Hence we can implement a **count-down timer** where in the sender can start it, respond appropriately to any timer interrupts & stop it.
	- Since packet numbers alternate, this iteration/protocol is aka **alternating bit protocol**.
	- Senders Side
		- ![[Pasted image 20240901165343.png]]
	- ![[Pasted image 20240903103608.png]]

### Pipelined reliable data transfer

![[Pasted image 20240904093520.png]]

- Stop and wait protocol is time consuming even if the bandwidth of the channel is big enough.

Example
- RTT is approx 30 ms
- Transmission rate, **R** is 1Gbps (10^9)
- Packet size, **L** is of 1,000 bytes (8000 bits)

With stop-and-wait, time to transmit the packet through the link is
```
d_trans = L/R = 8000 bits per packet / 10^9 bits per sec = 8 microseconds
```

Say the transmission begins at,
t = 0, then at `t = L/R` the last bit enters the channel at the sender side.

Then the receiver receives the last bit at,
`t = RTT/2 + L/R` = 15.008 msec

The receiver sends the ACK bit back at,
`t = RTT + L/R` = 30.008 msec

If we calculate the time the sender is actually busy sending the bits, (aka **utilization** of the sender),

```
U_sender = (L/R)/(RTT+L/R) = 0.008 / 30.008  = 0.00027
``` 

i.e. The sender is only able to send **276 KBps in a 1Gbps link**.

![[Pasted image 20240904094847.png]]

**Solution** to this problem is simple. The sender is allowed to send multiple packets at once rather than waiting for acknowledgement for each packet. This technique is know as **pipelining**.

Consequences of pipelining,
1. Must increase range of sequence numbers.
2. The hosts at both ends must buffer more packets.

![[Pasted image 20240904095239.png]]
#### GO-Back-N (GBN)

GBN protocol allows the sender to send packets at once, filling the pipeline, without waiting for acknowledgment from the receiver. The sender can send a max of **N** packets at once. N is also the **window size**.

> GBN is also referred to as the **sliding window protocol**

![[Pasted image 20240904095459.png]]


- **k** no. of bits -> Range of sequence numbers `[0, 2^k - 1]`
- After the range is over, and there are still packets remaining, the next sequence should start with `modulo 2^k - 1`.

Events,
1. **Invocation from above**
	- `rdt_send()` checks if the pipeline is full before sending data to the pipeline. The upper layer tries again if it's full. 
	- In real implementation, the data is probably buffered
2. **Cumulative Acknowledgement**
	- All packets upto "n" are taken to be a cum. ack. indicating that pakcets upto "n" have been received.
3. **Timeout event**
	- There is a single **timer**, which can be thought of as a timer for the oldest packet sent but not acknowledged.
	- If the timer runs out, sender resends all the packets that haven't been acknowledged.
	- If ACK is received, but there are still packets that aren't acknowledged, the timer **resets**.

![[Pasted image 20240904100508.png]]


Here's how it works:
1. **Sending Packets:** The sender transmits multiple packets within a window size.
2. **Receiving ACKs:** The receiver sends an ACK for each correctly received packet. If a packet is lost (like pkt2 in the diagram), the receiver will not acknowledge it, and it will continue to acknowledge the last correctly received packet (ACK1 in this case).
3. **Handling Duplicate ACKs:** When the sender receives multiple ACKs with the same sequence number (indicating that the receiver has not received the next expected packet), the sender understands that there may be a problem, but it does not take any immediate action.
4. **Timeout and Retransmission:** The sender waits for the timeout to occur. Once the timeout expires, the sender will retransmit the missing packet (pkt2) and all subsequent packets (pkt3, pkt4, pkt5) in the window.

In the above case if pkt2 wasn't lost but only got delayed (i.e would reach later but before the timer stops), then pkt3 would have been **buffered** and not retransmitted. But since pkt2 is lost, and no ACK2 is received within the timeframe, pkt2 is retransmitted.

> Note that if **k** packets have been received **in-order**, then it can be said that all packets upto **k** seq. number have been received (**cumulative acknowledgement**).

#### Selective Repeat

While GBN allows us to "fill the pipeline", it can get inconvenient at times when we are sending a large chunk of data and one of the packets get lost, then we'd have to send a large part of the data again, i.e. **retransmission of large data is inconvenient**.

Selective repeat protocol allows us to send only **selective part of the data that can be considered to be lost.** Again, a window size of **N** is maintained here.

In SR, the out of order packets are buffered until the correct ordered packets aren't received.

![[Pasted image 20240904101922.png]]

Sender events,
1. **Data received from above**, the receiver checks whether the incoming packet fits the senders window size, if not the packet is buffered and transmitted to the upper layer later. If it is, the packet is sent.
2. **Timer**, each packet has its own timer.
3. **ACK** received, when a packet is received, the receiver marks the sequence number. If the seq number matches the `send_base`, then the window is moved forward.

Receiver events,
1. For sequence numbers, `[rcv_base, rcv_base+N-1]`, i.e. the window size, ACK is returned to the sender, if packet wasn't received earlier, it's buffered.
2. For sequence numbers, `[rcv_base-N, rcv_base-1]`, packet is correct
3. Otherwise packet is incorrect

![[Pasted image 20240904104232.png]]

In the above diagram, since pkt2 is lost/not received, the next pkts are buffered. Once the timer for the pkt2 is over, ACK2 is sent, and the remaining buffered packets are sent to the upper layer.

Due to this, there is no synchronization between the receiver and the sender as to what has been correctly received and what has not.

![[Pasted image 20240904104914.png]]

## Connection oriented transport - TCP

- TCP is connection oriented because before the one end of the TCP connection can send data to the other end of the TCP connection, there must be a **handshake** between the processes.
- TCP connection is a **logical** connection which maintains the state residing only in the TCPs in the communicating end systems
- **Full duplex** service
- **Point to point**
- No **multicasting** support
- Before establishing a connection, client sends a segment first. The server then responds with a special segment to which the client again responds with a segment (**three way handshake**). The third segment may carry the *payload*.

*Flow*,
- Client process passes data through its **socket**.
- After passing through the socket, transfer of data is the responsibility of **TCP**.
- The client TCP will forward it to the **connection's send buffer**.
- Time to time, TCP (client) will collect chunks from the buffer and send it to the **network layer**. These segments are encapsulated separately to form **IP datagrams**. 
- When TCP receives the segment on the other end, the segment is stored in the **receiver's buffer**

> RFC 793 specifies that TCP should segments of data at its *own rate/convenience.*

- The max amount of data that can be grabbed and sent in a segment is called the **Maximum segment size (MSS)**.
- MSS is decided by checking the **length of the largest link layer frame** that can be sent by the local host. 

![[Pasted image 20240910195053.png]]

### TCP segment structure

![[Pasted image 20240910201308.png]]

> CWR and ECE are as **explicit congestion bits**.

#### Seq num and ack number

> In TCP, data is viewed as unstructured but ordered stream of bytes.

- critical part of TCP's reliable data transfer
- **Sequence number number for a segment** is the *byte-stream number* of first byte in the segment.
- Each sequence number is inserted in the **sequence number field** in the header of the appropriate TCP segment. As shown in the figure, *the first byte, of the data stream, is numbered 0. Then second segment is 1000, & so on.*
- **Acknowledgment** in TCP is complex. The *ack number* that host A puts in the segment, is the *sequence number of the next byte Host A expects from host B*. 
- Say that Host A sends the 1st segment with an ack number of 0, then the host B will send a response with ack number as 1000. Then Host A will send the next segment.
- Specifically, it tells the sender, *"I have received everything up to byte number X, and I expect the next byte to be X+1."*
- Now say that Host A has sent two segments at one after the other, but the Host B has sent ack for the *second* segment and not the first one. Host A will then resend the first segment with the same ack number.
- TCP is said to provide **cumulative acknowledgment**.

> If the segments have arrived out-of-order, TCP on its own doesn't need to do anything, it's up to the programmer on how they program it.

![[Pasted image 20240920182601.png]]

#### telnet case study

- application layer protocol
- designed to work b/w any pair of hosts
- data sent using telnet is not encrypted.
- ![[Pasted image 20240920184714.png]]
- The first segment carries the *starting sequence number* (assumed for client as 42) and *the acknowledgement number* (assumed for server as 79).
- Once the client receives this segment, it responds back with the seq number as 79 and ack number as 43 (stating it has received data upto 43 seq number).
- Then the host sends back another confirmation with the seq number incremented and ack number as 80, indicating it's expecting confirmation for data with seq number upto 80.
- *Purpose of second segment*
	1. acknowledgment that the data is received
	2. echo back the letter 'C'
- *Purpose of third segment*
	1. ack that data from server is received.

> The ack for client-to-server data is carried in a segment carrying server-to-client data, this ack is **piggybacked**  on the server to client data segment

### estimating round trip time

- `SampleRTT` - amount of time passed from when the segment was sent and the acknowledgment for that segment is received.
- SampleRTT is only measured for one segment that is currently acknowledged. It's also not calculated for retransmitted segments.
- It fluctuates from segment to segment due to *congestion protocol*, hence we need some average value.
- `EstimatedRTT` - average value of the previous SampleRTT's.

```
EstimatedRTT = (1-alpha)EstRTT - alpha(SampleRTT)
where alpha = 0.125
```

- ERTT takes the weighted average of SRTT where the most recent SRTT's have more weightage. This type of average called an **exponential weighted moving average**.
- It's also necessary to know how much SRTT deviates form ERTT.
- `DevRTT` - measures the deviation between SRTT & ERTT.

```
DevRTT = (1-beta)DevRTT * beta (|SRTT - ERTT|)
where beta = 0.25
```

- DRTT is also a *EWMA* of the difference b/w SRTT & ERTT, where little fluctuation in SRTT results in little fluctuation in DRTT, and high fluctuation in SRTT results in high fluctuation in DRTT.
- Now the **timeout interval**, it should be equal to ERTT plus some margin.
- `Timeout Interval` - time the client waits for an ack of a sent segment before assuming the seg was lost or delayed.

```
Timeout Interval = ERTT + 4 * DRTT
```

> When a timeout occurs, the timeout is *doubled* to avoid premature timeout again. Once the ack is received, ERTT is updated and so the interval is calculated again.


### reliable data transfer

> IP provides unreliable data transfer and doesn't guarantee in-order delivery. The bits can get lost, corrupted, shuffled & even overflow the routers.

- TCP creates a reliable data transfer service on top of IP.
- Earlier we kept an individual timer with each transmitted but unack segment. This creates **overhead**. Hence it's recommended to use a **single transmission timer**, even if there are multiple unacked segments.
![[Pasted image 20240920193822.png]]

```c
[Application Layer]
      |
      v
[TCP Sender] --- Creates Segment with Sequence Number ---> [IP Layer]
      |
      v
[Timer Starts] (If no other segment’s timer is running)
[Timer Expires] 
      |
      v
[TCP Retransmits Segment] --- Sends the same segment again ---> [IP Layer]
      |
      v
[Timer Restarts]
[ACK Received] (ACK number: y)
      |
      v
[TCP Checks SendBase]
      |
      v
IF (y > SendBase)
      |
      v
[TCP Updates SendBase to y] --- [SendBase = y] (Acknowledges all bytes before y)
      |
      v
[TCP Restarts Timer] (If there are pending unacknowledged segments)
```

- The above scenario is a highly simplified version of TCP. But it *also has issues*.
- Cases
	1. A sends segment -> B's ack is lost -> Timeout occurs -> A retransmits -> B sends ack again
	2. A sends two segs -> B sends ack for second and a random previous segment -> timeout for A's first seg -> A retransmits first seg but not second.
	3. A sends two segs -> B sends ack for first -> ack lost -> but before timeout, B sends ack for second -> second ack tells that everything before second ack is received (i.e. first segment is also received).

#### doubling time interval

- when a packet is retransmitted, the timeout is doubled from the its previous old value and not derived form ERTT & DRTT.
- this mod provides a limited form of congestion control.
- if the packets are retransmitted too many times, this might get congested.

#### fast retransmit

- problem with time interval, they can get too long and cause end-to-end delay. 
- this can be avoided by the sender detecting the packet loss by noting **the duplicate ACK's**.
- this dup ack tells the sender that the sender has already received ack for a segment
- ![[Pasted image 20240920195826.png]]
- sender often sends a chunk of data. if one of the data is lost, the receivers keeps on sending ack's for the lost data. 
- if the dup acks > 3, then the sender understands that the packet is lost and does a **fast retransmit**. i.e. retransmitting before the that segments timer expires.
- ![[Pasted image 20240920205203.png]]
- ![[Pasted image 20240920205213.png]]
- ![[Pasted image 20240920205223.png]]

#### error recovery mechanism

- selective acknowledgment allows a TCP to receiver to acknowledge out-of-order segments selectively rather than just cumulatively acknowledge the last correctly received, in-order segment.
- this is a hybrid of GBN & SR protocol.

### flow control

> tcp connections on the end systems maintain a receiver buffer from the applications read data one by one. they dont read it immediately when the buffer is being filled.

- tcp provides a **flow control protocol** service that eliminates the buffer from being overflowed by received data. aka a **speed matching service**.
- `Receive window or rwnd` - tells the sender about the available space in the receivers buffer. both end systems store this information.
- The receiver keeps track of a few variables
- `LastByteRead` - the number of the last byte read in the data stream from the buffer
- `LastByteRcvd` - number of the last byte received in the data stream that has arrived from network to *receive buffer*
- Since TCP doesn't allow overflow

```
LastByteRcvd - LastByteRead <= RcvBuffer
```

- therefor the rwnd is always calculated as the above values change, *rwnd is a dynamic value*.

```
rwnd = RcvBuffer - [LastByteRcvd - LastByteRead]
```

- The sender keeps track of two variables, `LastByteSent` & `LastByteAcked`. There difference is the amount of segments that are unacknowledged and sent by the sender.

```
LastByteSent - LastBytAcked <= rwnd
```

- Since the rwnd can get full or (rwnd = 0) and assuming the receiver doesnt want to send anything either. so when the rwnd != 0, the sender won't know this.
- So to overcome this, sender must send 1 byte messages to check whether rwnd != 0, once this is true, the byte is acked with the rwnd value.

### tcp connection management

initiation
1. the client side TCP sends a special segment to the server side TCP server. 
	1. doesn't contian application layer data
	2. contains a SYN bit set to 1.
	3. aka **SYN segment**
	4. client also choose random **initial sequence number**
	5. isn is packed with the IP datagram and sent to server
2. the server side receives the segment
	1. it dedcapsulates it and assigns allocates the TCP buffers and variables.
	2. it then sends a **connection-granted** segment back to client
	3. it includes the SYN bit set to 1
	4. it also includes a random **initial sequence number** (diff from client).
	5. no application layer data
	6. aka **SYNACK segment**
3. client allocates buffer and variables to connection
	1. sends a final ack segment
	2. ack is server_isn + 1
	3. SYN bit is 0.

> this is the **three-way handshake**.

termination,
1. client & server deallocate the buffer and variables.
2. the client process asks for termination
3. the tcp client sends a segment w/ the **FIN** bit set to 1.
4. the tcp server responds with ack, w/ **FIN** bit set to 1.
5. the tcp server again sends **a shutdown segment** with FIN bit set to 1, initiating a **shutdown**.
6. the client sends an ack segment.

states visited by client and server
![[Pasted image 20240920215514.png]]
![[Pasted image 20240920215522.png]]

> NMAP Port Scanning Tool 
> In our example, NMAP sends a SYN segment to port 6789.
> - TCP SYNACK from target host to source host means **open** port
> - TCP RST from target host to source indicates that the **port has no firewall**
> - No response means the SYN segment was **blocked** by a firewall

---

![[Pasted image 20241126181700.png]]

---
## principles of congestion control

1. **Feedback Mechanism**: Use feedback from the network (e.g., packet loss, delays) to adjust the sender's transmission rate dynamically. This helps avoid overwhelming the network.
2. **Window-based Control**: Implement mechanisms like TCP’s sliding window protocol, where the sender maintains a window of packets that can be sent before needing an acknowledgment. The window size can be adjusted based on network conditions.
3. **Congestion Avoidance**: Proactively manage traffic by reducing the transmission rate before congestion occurs. This can involve algorithms that gradually increase the sending rate until packet loss is detected.
4. **Rate Limiting**: Establish maximum sending rates for hosts to prevent excessive data flow into the network, ensuring fair access for all users.
5. **Load Balancing**: Distribute traffic across multiple paths or links to avoid congestion on any single path. This can involve techniques like multipath routing.
6. **Explicit Congestion Notification (ECN)**: Utilize network signals (like marked packets) to inform senders about impending congestion, allowing them to reduce their sending rates preemptively.
7. **Retransmission Strategies**: Implement strategies for retransmitting lost packets, considering both reliability and network congestion. Adjust retransmission times based on current network conditions.
8. **Fairness**: Ensure that all users and applications receive a fair share of the network resources, preventing any single user from monopolizing bandwidth.
9. **Adaptive Algorithms**: Use adaptive algorithms that can learn from past network behavior, optimizing the sending rate based on changing conditions.
10. **Monitoring and Analysis**: Continuously monitor network performance metrics (like throughput and delay) to inform congestion control decisions and improve overall network efficiency.

### Congestion Control in TCP

Congestion control is a crucial aspect of the Transmission Control Protocol (TCP) that manages *data flow to prevent network congestion*. It employs several algorithms and mechanisms to optimize throughput while minimizing packet loss. Key components of TCP congestion control include **Slow Start**, **Congestion Avoidance**, **Fast Recovery**, and others.

#### Slow Start
TCP Slow Start is the initial phase of TCP congestion control. When a TCP connection is established, the sender begins with a small congestion window (cwnd), typically set to one Maximum Segment Size (MSS). The sender gradually increases the cwnd exponentially with each acknowledgment (ACK) received. For instance, if the cwnd starts at 1 MSS, it doubles with every successful ACK until it reaches a predefined threshold known as the slow start threshold (ssthresh). This exponential growth continues until a packet loss is detected or the ssthresh is reached, allowing TCP to quickly probe the network's capacity without overwhelming it.

#### Congestion Avoidance
Once the cwnd surpasses the ssthresh, TCP transitions to the Congestion Avoidance phase. In this phase, **the increase in cwnd becomes linear rather than exponential.** The cwnd increases by one MSS for each round-trip time (RTT) of successful transmissions. This approach helps maintain a steady flow of data while avoiding congestion, as it allows for gradual adjustments based on network conditions.

#### Fast Recovery
Fast Recovery is an optimization technique used in conjunction with Fast Retransmit. When packet loss is detected via duplicate ACKs (typically three), TCP enters Fast Recovery instead of returning to Slow Start. The ssthresh is set to half of the current cwnd, and the cwnd is adjusted to this new value. This allows for quicker recovery from packet loss while maintaining some level of throughput, as it enables the sender to continue transmitting new data rather than restarting from a low cwnd.

#### TCP Splitting
TCP splitting involves dividing a single TCP connection into multiple parallel connections. This technique can enhance throughput and improve performance over high-bandwidth paths by allowing simultaneous data streams, effectively utilizing available bandwidth and reducing latency.

#### TCP Reno and TCP Tahoe
TCP Reno and TCP Tahoe are two versions of TCP that implement different strategies for congestion control. 

- **TCP Tahoe**: Utilizes Slow Start and Congestion Avoidance but returns to Slow Start after detecting packet loss.
- **TCP Reno**: Introduces Fast Recovery, allowing it to recover from packet loss without reverting to Slow Start, thus maintaining higher throughput.
Here’s a comparison of how TCP Tahoe and TCP Reno react to packet loss, specifically focusing on the scenarios of timeout and receiving three duplicate ACKs:

| Feature                          | TCP Tahoe                                              | TCP Reno                                               |
|----------------------------------|-------------------------------------------------------|-------------------------------------------------------|
| **Response to Timeout**          | Upon detecting a timeout, Tahoe reduces the congestion window (cwnd) to 1 MSS and sets the slow start threshold (ssthresh) to half of the current cwnd. It then enters the Slow Start phase to probe the network again. | Similar to Tahoe, upon a timeout, Reno also resets cwnd to 1 MSS and sets ssthresh to half of the current cwnd. It then restarts in Slow Start. |
| **Response to 3 Duplicate ACKs** | When three duplicate ACKs are received, Tahoe performs a Fast Retransmit, sets ssthresh to half of the current cwnd, and reduces cwnd to 1 MSS. It then enters Slow Start again, effectively restarting the congestion control process. | Upon receiving three duplicate ACKs, Reno performs Fast Retransmit but instead of reducing cwnd to 1 MSS, it halves the cwnd and sets ssthresh equal to this new value. Reno then enters Fast Recovery, allowing it to continue sending new data without returning to Slow Start immediately. |
| **Performance During Recovery**  | Tahoe's approach can lead to slower recovery from packet loss since it resets cwnd and starts over from a low value, which can significantly reduce throughput in congested networks. | Reno's Fast Recovery allows it to maintain a higher throughput during recovery since it does not reset cwnd completely but rather halves it, enabling quicker adaptation to network conditions. |
| **Efficiency**                   | Less efficient in high-loss environments due to frequent resets to Slow Start after packet loss detection. This can lead to underutilization of available bandwidth. | More efficient in handling packet loss scenarios as it maintains a higher cwnd during Fast Recovery, allowing for better utilization of network resources. |

![[Pasted image 20240921122340.png]]
#### Additive Increase Multiplicative Decrease (AIMD)
AIMD is a fundamental principle behind TCP congestion control. It combines additive increase (gradually increasing cwnd) with multiplicative decrease (halving cwnd upon detecting congestion). This balance allows TCP to efficiently utilize network resources while responding appropriately to congestion signals.

#### Macroscopic Description of TCP Throughput
The throughput of TCP can be described on a macroscopic level as being influenced by factors such as round-trip time (RTT), packet size, and network conditions. The throughput tends to stabilize around a certain value determined by these factors, demonstrating how effectively TCP can adapt to varying network environments.

w - window 

![[Pasted image 20240921124720.png]]

#### TCP Over High-Bandwidth Paths
When operating over high-bandwidth paths, traditional TCP may not fully utilize available bandwidth due to its conservative nature in adjusting cwnd. Techniques such as increasing the initial cwnd size or implementing larger MSS can help improve performance on high-speed networks.

#### Fairness
Fairness in network protocols refers to how bandwidth is allocated among multiple users or connections. In the context of TCP, fairness ensures that all connections receive an equitable share of available bandwidth without one connection monopolizing resources.

#### Fairness and UDP
Unlike TCP, which implements congestion control mechanisms, User Datagram Protocol (UDP) does not have built-in mechanisms for fairness or congestion management. This can lead to scenarios where UDP traffic can dominate available bandwidth without regard for other traffic types.

#### Fairness and Parallel TCP Connections
When multiple parallel TCP connections are established between two endpoints, they must compete for shared resources. Properly designed algorithms ensure that these connections fairly share bandwidth while minimizing congestion effects on overall performance.

#### Explicit Congestion Notification (ECN)
ECN is an advanced mechanism that allows routers to signal impending congestion to endpoints before packet loss occurs. By marking packets instead of dropping them, ECN enables senders to reduce their transmission rates proactively, enhancing overall network stability.

#### Network-assisted Congestion Control
Network-assisted congestion control involves utilizing feedback from network devices (like routers) to inform endpoints about current network conditions. This feedback can help adjust transmission rates more effectively than traditional end-to-end methods alone, leading to better utilization of network resources and reduced congestion.

These principles collectively form a comprehensive framework for managing data flow in networks using TCP, ensuring efficient communication while minimizing potential issues related to congestion.