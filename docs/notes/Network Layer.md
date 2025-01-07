Index

- [[Network Layer#4.1 overview |Overview]]
	- [[Network Layer#4.1.1 forwarding and routing data and control planes|forwarding and routing data and control planes]]
		- [[Network Layer#control plane|control plane]]
		- [[Network Layer#control plane sdn approach|sdn approach]]
	- [[Network Layer#4.1.2 network service model|network service model]] 
- [[Network Layer#4.2 inside a router|Inside a Router]]
	- [[Network Layer#4.2.1 input processing & destination based forwarding|input processing & destination based forwarding]]
	- [[Network Layer#4.2.2 Switching fabric|switching fabric]]
	- [[Network Layer#4.2.3 output processing|output processing]]
	- [[Network Layer#4.2.4 queueing|where does queuing occur]]
	- [[Network Layer#4.2.5 packet scheduling|packet scheduling]]
- [[Network Layer#4.3 ip thing|The Internet Protocol (IP): IPv4, Addressing,IPv6, and More]]
	- [[Network Layer#ipv4 datagram| IPv4 Datagram Format]]
	- [[Network Layer#data fragmentation|IPv4 Datagram Fragmentation]]
	- [[Network Layer#ipv4 addressing|ipv4 addressing]]
- [[Network Layer#4.4 generalized forwarding & sdn|Generalized Forwarding and SDN]]
- [[Network Layer#Middleboxes|Middleboxes]]
- 

## 4.1 overview
- transport layer -> segment
- segment -> network layer
- encapsulated into a datagram -> sender router
- receiver router takes datagram -> network layer
- breaks datagram back to segment -> network layer
- **routers dont run on application and transport layer protocols**

roles,
- primary **data-plane** role of router is to forward datagrams
- primary **network control plane** role is to coordinate these local, per router forwarding actions.

### 4.1.1 forwarding and routing: data and control planes

network layers has two *main functions*

- **forwarding**, when a packet arrives at a router's input link, the router must move the packet to the appropriate output link. a packet can also be blocked from exiting the router
	- refers to **router-local action**
	- implemented direcly in hardware
	- takes place at very short timescale
- **routing**, network layer must *determine* the **route/path** taken by packets as they flow from *sender to receiver*. the algo's used to figure out the routes are **routing algorithms**.
	- refers to **network-wide** process
	- implemented in software
	- takes place at large timescales

A key element of the router is the **forwarding table**. router examines fields of recv packet header and using these values it indexes into its table. The table helps *determining the output link* for this packet.


#### control plane

routing algorithms in the control plane determine the *contents* of the router's forwarding table. these algo's, for now, are present in each router and each router contains the forwarding and routing functions.

*the algo's communicate* with each other to share *routing information* through a *routing protocol*.

![[Pasted image 20241003094622.png]]

#### control plane: sdn approach

in this approach, the routing algorithm, or the control plane which contains this algorithm, is built seperately from the router, this is the **remote controller**.

the control plane is built in remote data centers that are controlled by ISP's or third party services. the router then communicates with this remote controller exchanging the forwarding table data and other router information.

here, router -> forwarding function, remote controller -> routing function.

This setup is known as the **software defined networking** approach. this is more reliable and redundant. in recent times this software has been made open sourced.

![[Pasted image 20241003095215.png]]

### 4.1.2 network service model

the **network service model** defines the *characteristics* of *end-to-end* delivery between sending and receiving hosts.

the services provided are,
1. guaranteed delivery
2. guaranteed delivery with bounded delay
3. in order packet delivery
4. guaranteed minimal bandwidth
5. security

the network layer provides a single service known as a **best effort service**. this means that the network layer can never guarantee any of the above mentioned services.

even though it doesn't guarantee the services, the internets best service model combined with adequate bandwidth provisioning is proven to be more than *good enough*.

> - Simplicity of mechanism has allowed internet to be widely deployed adopted.
> - sufficient provisioning of bandwidth allows performance of real time applications to be good enough for most of the time
> - replicated, application layer distributed services connecting close to clients' network, allow services to provided from multiple 

## 4.2 inside a router
![[Pasted image 20241007135929.png]]

- **input port**
	- terminates an incoming physical link at a router (shown at both ends) (this is a **physical layer function**)
	- also performs **link layer functions** (rep by middle boxes and input & output ports)
	- **lookup function** (rightmost box of input port). the forwarding table is consulted where to send the packet to the right output port via the *switching fabric*
	- *control packets* from ip port (packets w/ routing info) -> routing processor
- **switching fabric**
	- connections ip port to op port.
- **output port**
	- stores packets recv from s/w fabric
	- sends packets to approp links using link & physical layer functions.
- **routing processor**
	- control-plane functions
	- executes router protocols
	- maintains tables & attached link state information
	- computer forwarding table
	- in SDN, it communicates with the *remote-controller* to write down the receiving forward table entries computed by remote controller, into the routers input ports.

types of forwarding,
- Destination based forwarding
- General forwarding

this architecture is almost always implemented in hardware. this is the **dataplane**, which operates at a nanosecond scale.

whereas, the **control plane** is implemented in software operating at a millisecond or second scale.

### 4.2.1 input processing & destination based forwarding

The input port's 
- **line terminal** function 
- and **link-layer processing** implement the physical and link layer functionalities for that input link. 
- Further, the **lookup performed** is central to the router's operation.


In lookup, the forwarding table used is either computed by the routing processor or sent by the SDN remote controller. This table is copied from *the routing processor -> line cards* over a separate bus.

At each line card, a **shadow copy** is performed, this is done to reduce the *centralized processing bottleneck*, i.e. it doesn't invoke the routing processor on a per packet basis.

> Considering a simple case where the packet received is matched in the table and then forwarded to the appropriate link based on the match. Would this mean we would need to store details of each packet in the table? This would not be optimal.

Routers match something called **prefix** of the packets destination address with the entries in table. If there is any match, the packet is forwarded to a link associated with the match.

![[Pasted image 20241014094753.png]]
In this figure, the otherwise states that if there are no matches the *default interface* will be 3.

Lets say there is a case when a single packet matches with multiple entries in the table, i.e. the first 21 bits of a packet could match the 3rd entry and the first 24 bits could match the 2nd entry. In this case, the router uses the **longest prefix matching rule**.

> **Prefix matching** - n bits of a address match n bits in table
> **Longest Prefix Matching** - n bits of an address match n bits of two entries in table, then choose the longer entry.

These lookups are essentially simple to build (**loopkup - search through forwarding tables**), but for larger links, they must be fast. Some chips that are used,
1. DRAM and faster SRAMs
2. Ternary Content Addressable Memories (TCAM) (returns in constant time)

Once the *packets link is determined*, it's sent to the **switching fabric**. Here it can also be *queued* at the input port due to other links currently using the fabric.

> Although the most important action is the lookup, there are several other key things that need to be taken care of,
> 1. The physical and link layer processing
> 2. the packets version number, check sum and time-to-live field
> 3. **counters** used for network management

> **Matching** - input ports steps of lookup up a dest IP address
> **Action** - sending the packet to s/w fabric to the specified output
> The above terms together, *match plus action* abstraction, are performed in almost all network devices and not just routers.

### 4.2.2 Switching fabric

this is the very heart of the router as this is the place the packets are switched from an input port to an output port. 

1. **Switching via memory**
	- simplest form of early routers
	- earlier, cpu was the routing processor
	- input/output ports were like traditional I/O devices
	- i/p signaled the routing processor via interrupt
	- packet copied (from ip) -> processor memory
	- packet's header -> destination address extracted -> lookup performed to get op port -> packet copied into op port
	- if memory bandwidth is B, then **throughput is B/2**
	- this lookup and storing in memory is done on the **input line cards**
	- two packets can't be forwarded at once
1. **Switching via bus**
	- input port directly forwards packet to output port (w/o routing processor intervening).
	- packet is pre-pended with a switch-internal label (header).
	- all o/p ports receive the packet but the packet with the matching label is kept and others are discarded
	- once the packet reaches the op port, the label is also discarded
	- one packet can cross the bus at a time.
1. **Switching via an interconnection network**
	- to overcome bandwidth limitation, use sophisticated network
	- **crossbar** switch with 2N buses (N ip, N op)
	- each vertical bus connects with a horizontal bus.
	- two packets can cross at the same time if they have different ip & op buses.
	- crossbar switch is **non-blocking**, the packet is not blocked from reaching its output port as long as no other packet is going to that output port.
	- More sophisticated interconnection networks use multiple stages of switching elements to allow packets from different input ports to proceed towards the same output port at the same time through the **multi-stage switching fabric**.
	- **Non-Blocking Switching**:
	    - The Cisco CRS router uses a "three-stage non-blocking switching" method.
	    - Non-blocking means that packets can always find a path to their destination without getting stuck due to other traffic.
	- **Scaling Switching Capacity**:
	    - A router can increase its capacity by using multiple switching fabrics in parallel.
	    - This parallel setup allows for greater data handling capabilities.
	- **Packet Splitting and Reassembly**:
	    - In the parallel fabric approach, an input port divides a packet into smaller pieces or "chunks."
	    - These chunks are then distributed ("sprayed") across multiple switching fabrics (K chunks sent through N fabrics).
	    - At the output port, these chunks are reassembled into the original packet for delivery.

![[Pasted image 20241014114430.png]]


### 4.2.3 output processing

the packets stored in the output ports memory are taken and forwarded to the output link. this includes queueing, de-queueing and physical & link layer processing.

### 4.2.4 queueing

by our discussion earlier it's clear that both the input & output ports have a queue where the packets are stored for time being, but lets consider a scenario where the packets increase in number and the routers memory is eventually exhausted. Due to the memory being exhausted, there could be **packet loss**.

> input/output transmission rate -> $R_{line}$
> Switching fabric transfer rate -> $R_{switch}$

#### input queueing

if switch fabric is not fast enough to transfer all arriving packets through the fabric without delay, then **input queuing** occurs, i.e. packet queue up on input ports.

considering the switching via a cross-switch, the packet won't experience any blocking unless two packets have the same destination (output ports). But there might be a case where a packet (p2) which is meant to go to, say, output port A, and the packet which is right infront of it (p1), needs to go to, say, output port B, but the port B is currently busy with another packet (pk) hence keeping p1 in waiting. This causes p2 to kept in waiting due to p1. This is called **head-of-line (HOL)** blocking.

> HOL - a queued packet in an input queue must wait for transfer through the fabric (even though its output port is free) because it is blocked by another packet at the head of the line (in-front of it).

#### output queueing

if $r_{switch}$ >> $r_{line}$ of the output port, then the packets sent by the swtich will be sent faster than the rate at which output port sends packets to its appropriate links.

when there is insufficient memory, a decision to drop the packet (arriving or queued) must be made. this policy is called the **drop-tail**.

at times, packets are dropped before the memory gets full, this is done to send the sender a signal for congestion. multiple policies for *proactive packet* dropping & *marking policies* have been analyzed (**Active Queue Management**). Most widely studied AQM -> **Random early detection**.

### 4.2.5 packet scheduling

question of determining the order in which queued packets are
transmitted over an outgoing link.

#### FIFO

- based on first come first serve basis priority
- packets are transmitted based on the order they have arrived
- the queue-packet discarding policy determines whether *packet is dropped or whether packet is removed from the queue*.
![[Pasted image 20241108001312.png]]
#### priority queue

- incoming packets are classified according to *priority classes*
- operators configure a queue to give higher priority to **network management information** packets
- similarly, voice-over-IP packets >> non-real traffic (SMTP, IMAP)
- the individual queues operate in *fifo* manner
- say a low priority packet is already under transmission when a higher priority packet arrives, the lower priority transmission *is not* stopped (**non-preemptive priority queue**).
![[Pasted image 20241123141515.png]]

#### round robin

- queued just like in priority queue
- here classes are given equal opportunities to transmit packets, this is the **work conserving queuing** discipline.
- this discipline checks for packets in the current class, if it finds none, it checks the next class

![[Pasted image 20241123141648.png]]

##### weighted fair queuing

- packets are classified and queued in appropriate per-class waiting area
- serve classes in circular manner (1 -> 2 -> 3 -> 1 and so on)
- also *work conserving*
- each class receives a *differential amount of service in any interval of time*.
- class i -> weight i
- in any time interval, class i -> guaranteed $w_i/(\sum w_j)$ fraction of service
- for transmission rate R, class i -> guaranteed $R*w_i/(\sum w_j)$ throughput

![[Pasted image 20241123141952.png]]

## 4.3 ip thing

### ipv4 datagram
- network layer packet is called **datagram**.
- **version number**
	- 4 bits
	- defines version of IP datagram
- **header**
	- 20 byte header
	- defines where the payload actually starts in the datagram
- **type of service (tos)**
	- helps differentiate between datagrams
	- Example of TOS -> Explicit Congestion Notification (6:7) 
- **datagram length**
	- 16 bits long
	- length of datagram (header + data) - measured in bytes
	- max size of datagram - $2^{16} - 1$ = 65,535 bytes
- **identifier, flags, fragmentation offset**
	- used for IP fragmentation
- **time to live**
	- ensures that datagram isn't re-processed and doesn't circulate forever
	- TTL is *decremented* everytime datagram is processed.
- **protocol**
	- indicates which protocol should datagram be passed to in transport layer
	- 6 - TCP, 17 - UDP
- **header checksum**
	- detects error bits
	- router computes header checksum -> checks if there are any errors -> if errors exist datagram is discarded.
- **source and destination IP address**
- **options**
	- allows header length to be extended
	- complicates things as we don't know what the size of the header would be.
![[Pasted image 20241124120357.png]]
> IP Datagram header - 20 bytes
> IP Datagram header w/ TCP segment -40 bytes (20 + 20 w/ application layer message)

### data fragmentation

- datagrams have variable size
- datagrams pass through the link layer where they are *encapsulated* into **link layer frames**.
- link layer has a *maximum amount of data* that can pass through it (**maximum transmission unit (MTU)**)
- different end-systems have different link layer protocols, having *different MTU's*
- for the case where **MTU << Datagram size**, we perform **fragmentation** of the Datagram.
- Datagram -> Sub-datagrams -> each sub-datagram is assigned source address, identification number (of original datagram) & destination address.
- Sending host usually increments identification number for each datagram it sends.
- First sub-datagram -> **Flag bit: 1**, Last sub-datagram -> **Flag bit: 0**.
- Destination address checks **id number** to check if datagram is part of a bigger datagram. 
- To order them properly, the **offset field** is checked

### ipv4 addressing

- ip addresses aren't associated with devices.
- IP address are **32 bit** identifiers associated with **interfaces**
- **Interfaces**: connection between host/router and physical link.

#### subnets

- IP addresses must be *unique*. A part of the address is defined by something called **subnet**.
- **A subnet** is a network inside a network which allows *device interfaces* to interact *without intervention of a router*.
- Each isolated network is *a subnet*.
- IP addresses -> Subnet Part (common for a given subnet) + Host Part
- Example![[Pasted image 20241124231727.png]]
- In this isolated n/w, **"223.1.3"** part is same for all devices, this is the *subnet* part. The **"/24"** indicates that 24 bits are the same.
- **Classless Interdomain Routing (CIDR)** is the *slash notation*, it's the subnet portion of the address of arbitrary length.

> How does the host get its address in an isolated network?
> How does a subnetwork get its address in an global network?

#### obtaining a block of address

- allotted by the networks ISPs address space.
- ![[Pasted image 20241124234143.png]]
- The ISPs block is divided into 8 address spaces.
- These 8 organizations contact the global internet through the main *ISP block addr*.
- This is called **address aggregation**
- ![[Pasted image 20241124235151.png]]
- say one organization wants to change its ISPs but also keep the address range, then the new ISP will expect its original range and also the new range from the shifted organization
- ![[Pasted image 20241124235202.png]]

#### how ISPs get a block of address

- provided by ICANN
- ICANN allocate IP addresses through 5 **regional registries**
- then RRs assign to Local registries.
#### obtaining a host address
- protocol used is **Dynamic Host Configuration Protocol (DHCP)** which allows automatic configuration of IP addresses to devices.
- This method is far better and efficient then earlier methods where the *sysadmin used to hard-code the address in the config file*
- when device joins a n/w, for it (the device) to get an ip addr,
	- n/w needs to have **DHCP server**. The host requests and receives an addr through this server by using *DHCP protocol*.
	- when the device leaves, the IP addr is given up. (Can be re-used/reclaimed).

> DHCP server runs on **port 67 and UDP**

- Four types of msg's b/w an incoming device and DHCP server
	- **DHCP discover**
		- msg broadcasted by incoming device to check if DHCP server exists
	- **DHCP offer**
		- msg broadcasted by server containing a suggested IP address the incoming device can use.
	- **DHCP request**
		- msg broadcasted by incoming device which contains the server proposed address or device wants to renew an old address
	- **DHCP ack**
		- msg broadcasted by server which acknowledges the request and grants the device the particular ip address.

In this example, DHCP -> port 67, client -> port 68![[Pasted image 20241124233756.png]]

### NAT

- all devices within a LAN will have the ip address from a specific range of addresses, known as **private addresses**.
- datagrams within the local network work with the private ip's.
- but for datagrams to communicate with the global network, they use a static **NAT IP address**.
- **Implementation**
	- Outgoing datagrams: (source IP address, port #) -> (NAT ip address, new port #)
	- Remember in NAT translation table: every mapping of the old port number to the new port number must be done in the table.
	- Incoming datagrams: (NAT ip addr, new port #) -> (source ip address, port #)
- ![[Pasted image 20241125001113.png]]

### ipv6

- provided larger address space
- notion of **flow** (connection b/w end-points) was introduced with ipv6
- **source addr and destination addr**
	- 32 to 128 bits
	- new type of address, **anycast** address
- **flow label**
	- identify datagrams between "flows"
- **priority / traffic class**
	- 8 bit
	- to give priority to certain datagrams in a flow
	- like TOS in ipv4
- **payload length**
	- 16 bit
	- number of bytes in IPv6 datagram.
- **next header**
	- identifies to which protocol the datagram is being transferred to
- **hop limit**
	- contents are decremented by one by each router that forwards the datagram.
	- if content = 0, datagram discarded
	- similar to TTL
- no **checksum**, **fragmentation**, **identification number**, **flag**, **options**

![[Pasted image 20241125004728.png]]

#### ipv4 to ipv6

- both versions try to coexist
- some are ipv4 or ipv6 or both.
- **tunneling** allows this to happen
- in tunneling, *a datagram inside a datagram* is transferred. 
- **ipv6 datagram inside the ipv4's payload**
- say we have two ipv6 routers, which also can do ipv4 stuff\
	- we would usually connect them using an ethernet cable, but we can also connect them using *a network of ipv4 routers*, this network is called the **tunnel**. 
	- the datagrams b/w ipv6 routers is passed through the tunnel.
	- the datagram is an **ipv4 datagram** w/ the payload as the **ipv6 datagram**.
- Physical view![[Pasted image 20241125005802.png]]

## 4.4 generalized forwarding & sdn

- recall *match plus action*
- in **generalized forwarding**, match-plus-action table generalizes the notion of destination based forwarding table.
- here a **flow** table is used. it consists of a **match** and a corresponding **action**.
- most popular implementation of this is **OpenFlow**

### OpenFlow

#### Match
- in OpenFlow, there are a set of header that are to be matched
- ![[Pasted image 20241125112654.png]]

#### Action
- for a given match, we can perform multiple actions
- **Forwarding**
- **Dropping**
- **Modify-field**

## Middleboxes

- **Any intermediate box, that performs functions apart from normal, standard functions of an IP router on the data path between source host and destination host**
	- Standard functions of an ip router -> forwarding
	- data path -> meaning middlebox is a network layer thing
- Example
	- **NAT**/**Firewalls** using generalized based forwarding
	- **Load Balancers** (aka layer 7 switch)
	- **Caches**
	- **CDN's**
- Is a **white-box hardware** which implements open APIs
	- Example is Network function virtualization