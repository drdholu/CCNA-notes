# What is the Internet

## Nuts and Bolts Description

The internet is a computer network that interconnects billions of computing devices throughout the world. On the internet, all of these devices are called **hosts or end systems**.

![Internet Diagram](Pasted%20image%2020240719174112.png)

End systems are connected to each other by a network of **communication links (coaxial, copper, optical, etc.) and packet switches**. Different links can transmit data at different rates.

> Transmission rate of a link is measured in bits/second.

When data is to be transferred, it isn't just sent as raw data; it's modified and merged with additional information required by both end systems.

**Sending data system => Data + Header bytes (**packets**)**

The receiving end system receives the packets and de-structures them to get the original data.

How does this packet reach the receiver end system though? Something called a **packet switch** is used to **forward** the packet received and send it to the appropriate end system based on the headers present.

The two most prominent types of packet switches are **Routers** and **Link-Layer switches**.

> **Routers** => Access networks (An access network is a type of network which physically connects an end system to the immediate router (also known as the “edge router”) on a path from the end system to any other distant end system.)
>
> **Link Layer Switches** => Network core (Part of a telecom system that connects users and the network)

The sequence of communication links and switches that are traversed by a packet is known as a **route** or **path**.

The internet provided to the End systems allows the transfer of data. The internet is provided through **Internet Service Providers (ISPs)**.

Essentially, each ISP in itself is a network of packet switches and communication links.

Examples of some network access provided by ISPs are Cable modem/DSL & mobile wireless access.

Now there are multiple tiers of ISPs that have to be interconnected to provide internet access.

1. Lower Tier
2. National Tier
3. International Upper Tier

Each of these ISPs are managed and runs the IP Protocol and adheres to the standards that are set by some organizations.

The IP protocol mentioned above isn't the only protocol, and it isn't only run by ISPs. End systems, switches, and other devices present on the internet also run the protocols.

The two most important protocols on the internet are:
1. Transmission Control Protocol (TCP)
2. Internet Protocol (IP)

These two protocols are collectively called **TCP/IP**.

As for the standards mentioned, the Internet standards are developed by the **Internet Engineering Task Force (IETF)**.

The IETF standards documents are called **Request for Comments (RFCs)**. RFCs define protocols such as TCP, IP, HTTP, etc. There are currently 7,000 RFCs.

## Services Description

The previous section described the internet using its components. But we can also describe it as an **infrastructure that provides services to applications**.

Traditional applications (email, web surfing, messaging, maps, etc.) are said to be **distributed applications** since they involve **multiple end systems** that exchange data with each other.

The internet primarily runs on the end systems, and the packet switches help with the exchange of data among end systems.

But how do these applications run on the end systems? To even make an application first, we need to **write it using a language such as C, JAVA, etc.** 

These programs can be run on end systems independently, but for these programs to transfer data and communicate, we need something called a **socket interface**, which is provided by the Internet.

The socket interface specifies how a program on the end systems asks the Internet to deliver data to a specific destination.

Again, this socket interface also has to follow a set of rules that the sending program must follow.

## What is a Protocol?

A protocol is exactly what it says; all activity on the internet that involves two or more communicating remote entities is governed by a protocol.

A simple example where a protocol is used is on the Web. When we first type the URL of a Web page into a web browser, the computer sends a request to the server. The server receives this and sends an **OK** for the connection. Following this, the computer then sends a **GET** request to the server to load the web page. At last, the server returns the web page to the computer.

> A protocol defines the format and the order of messages exchanged between two or more communicating entities, as well as the actions taken on the transmission and/or receipt of a message or other event.


![Protocol Example](Pasted%20image%2020240719184457.png)

## network edge

Internets end systems includde desktop computers, servers & mobile devices. End systems are also referred to as hosts. Usually they are further divided into client and server. client -> user desktops & etc, server -> data centers.

### access networks

The network that physically connects an end system to the first router on a path from one end system to another distant end system.

Types of broadband residential acces
1. Digital subscriber line
2. cables

DSL,
- users DSL is also used as the ISP.
- DSL modems use the existing connection lines (twisted pair copper wire) to exchange data with the digital subscriber line access multiplexer (DSLAM) to send it to the local control office.
- The DSL takes data -> high frequency tones -> CO.
- The high frequency tones are then converted back to the data.
- The line is encoded in three different frequences
	1. High speed downstream
	2. Medium speed upstream
	3. Ordinary two way telephone
- On customer side, **Splitter** splits the telephone signals and the data and forwards it to the DSL modem.
- DSL standards have different upstream and downstream rates.
- The rate also depends the distance between the homes and CO.


- Cable internet access makes use of existing cable telivision lines
- these cables connect to neighbourhood junctions to about 500-5,000 homes.
- since these cables provide fibre and coaxial cables, it's aka **Hybrid fibre coax (HFC)**.
- cia requires **modems**. 
- **cable modem termination system (cmts)** (similar to dslam) converts analog signal into digital.
- cable modems divide hfc into upstream and downstream with similar transfer rates. 
- cia is **shared broadcast medium**
- ![[Pasted image 20240921082004.png]]

- fiber to the home **ftth**, a new emerging technology
- optical fiber direct from CO.
- types -> direct fiber
- one fiber split into multiple for multiple houses.
- technique -> active optical network & passive optical networks
- FTTH using PON architecture involves an **optical network terminator** at each household which is connected to a *splitter*. this splitter connects multiple connections to one fiber line which meets at the **optical line terminator**
- ![[Pasted image 20240921083129.png]]
#### ethernet and wifi
- **Ethernet LAN** is the most common access technology in corporate, university, and home networks.
- **Ethernet users** use twisted-pair copper wires to connect to an **Ethernet switch**, which is connected to the larger Internet.
- Ethernet access provides **100 Mbps or 1 Gbps** for users and **1 Gbps to 10 Gbps** for servers.
- **Wireless LAN (WiFi)** based on **IEEE 802.11** technology is widely used in places like universities, offices, cafes, homes, and even airplanes.
- WiFi access is typically available within a few **tens of meters** from the access point.
- **802.11 (WiFi)** provides shared transmission rates of more than **100 Mbps**.
- **Ethernet and WiFi** were originally deployed in enterprise settings but are now common in **home networks**.
- Many homes combine **broadband access** (via cable modems or DSL) with **WiFi** to create home networks.
- A typical **home network** consists of:
  - A **wireless access point (base station)** for WiFi.
  - A **router** connecting the base station and wired devices to a **cable modem** for Internet access.
  - Devices such as laptops, wired PCs, and other wireless devices connected through the network.
#### 3g and lte

- **Mobile devices** like iPhones and Android phones are commonly used for messaging, photo sharing, watching movies, and streaming music on the go.
- These devices use **cellular networks** to send/receive packets through a **base station** operated by cellular network providers.
- **Cellular networks** offer a wider range than WiFi, with users needing to be within **a few tens of kilometers** of the base station (compared to WiFi's range of tens of meters).
- **3G wireless technology** provides **packet-switched wide-area Internet access** at speeds greater than **1 Mbps**.
- **4G technology**, such as **LTE (Long-Term Evolution)**, offers even higher-speed wide-area access, with reported rates exceeding **10 Mbps**, and downstream speeds in commercial deployments reaching **tens of Mbps**.
- **LTE** has evolved from **3G technology** and is part of the **4G wireless network** generation.

### physical media

- **Network access technologies** use various **physical media** for data transmission, such as HFC using **fiber and coaxial cable**, DSL and Ethernet using **copper wire**, and **mobile networks** using the **radio spectrum**.
- A **physical medium** refers to the method through which bits are transmitted via **electromagnetic waves** or **optical pulses** between transmitter-receiver pairs along a communication path.
- A **bit** travels through a network by passing between a series of **transmitter-receiver pairs**, using different types of physical media along the way.
- Common physical media include:
  - **Twisted-pair copper wire**
  - **Coaxial cable**
  - **Multimode fiber-optic cable**
  - **Terrestrial radio spectrum**
  - **Satellite radio spectrum**
- Physical media are divided into:
  - **Guided media**, where signals travel through a solid medium like **fiber-optic cables**, **twisted-pair copper wire**, or **coaxial cable**.
  - **Unguided media**, where signals propagate through the **atmosphere** or **outer space**, such as in **wireless LANs** or **satellite channels**.
- The **cost** of the physical medium (wires, cables) is often much **lower** compared to the **labor cost** for installation.
- To reduce future costs, builders often install **multiple media types** (twisted-pair, fiber, coaxial) in buildings even if only one medium is used initially.

## network core

### packet switching

- end systems exchange messages with each other
- to send messages, the  message is broken donw into smaller cunks of data called packets. 
- the packets are sent through comunication links & packets switches.
- packets are transmitted over the links at a rate equal to the full transmission rate of the link
- i.e. L/R where L = a packet of L bits and R = transmission rate bits/sec
#### store and forward
- **Store-and-forward transmission**: Routers store a packet’s bits before forwarding it, meaning they must receive the entire packet before transmitting it to the next link.
- **Example setup**: A simple network has two end systems connected by a router. The source has three packets of **L bits** to send to the destination.
- **Total delay for a single packet**: The source starts transmitting at time 0, and by time **L/R seconds**, the router has received the full packet and can start transmitting. At **2L/R seconds**, the destination has received the entire packet. Total delay for one packet is **2L/R**.
- **Alternative approach**: If routers forwarded bits immediately as they arrived (without storing the entire packet), the delay would be reduced to **L/R**, since bits wouldn't need to wait at the router.
- **Multiple packets scenario**: When sending multiple packets:
  - At **L/R seconds**, the router forwards the first packet and the source starts sending the second packet.
  - At **2L/R seconds**, the destination receives the first packet and the router receives the second.
  - At **3L/R seconds**, the destination receives the first two packets and the router receives the third.
  - At **4L/R seconds**, the destination receives all three packets.
- **General case**: For a path with **N links**, the total end-to-end delay for a single packet is determined using the same logic and increases with the number of routers (N-1 routers between source and destination).
- ![[Pasted image 20240921090708.png]]

#### queuing delays & packet loss
- **Output buffer (output queue)**: Each packet switch has an output buffer for each attached link. This stores packets waiting to be transmitted into that link.
- **Buffer role in packet switching**: If a packet arrives and the link is busy, the packet waits in the output buffer, leading to potential queuing delays.
- **Queuing delays**: These delays are variable and depend on network congestion. If the buffer is full, packet loss may occur, either dropping the arriving packet or an already-queued one.
- **Packet loss**: Occurs when the buffer is full due to congestion. Either the new packet or one of the queued packets is dropped.
- **Figure 1.12 example**: Hosts A and B send packets to Host E over 100 Mbps Ethernet links, which then funnel into a 15 Mbps link. Congestion occurs if the arrival rate exceeds 15 Mbps.
- **Queuing example**: If Hosts A and B each send a burst of packets simultaneously, most of the packets will wait in the output buffer queue, analogous to real-life queuing situations like at a bank teller or tollbooth.
- **Queuing delay**: Further analyzed in Section 1.4.
- figure 1.12![[Pasted image 20240921091050.png]]

#### Forwarding Tables and Routing Protocols
- **Router forwarding**: Routers forward packets arriving on one link to another based on the packet’s destination address, which is an IP address in the Internet.
- **IP address structure**: The IP address is hierarchical, similar to postal addresses. The source end system includes the destination IP address in the packet’s header.
- **Forwarding process**: When a packet arrives at a router, the router examines part of the destination address and uses its forwarding table to determine the appropriate outbound link.
- **Forwarding table**: This table maps destination addresses (or portions of them) to outbound links. The router consults this table for each incoming packet.
- **Analogy**: The process is compared to someone asking for driving directions at each stop along the way, extracting portions of the destination address (city, street, house number) at each step, with routers acting as "gas station attendants."
- **Routing protocols**: Forwarding tables are automatically set using special routing protocols, which determine the shortest path between routers and configure the tables accordingly. This is explored in Chapter 5.
- **Traceroute tool**: The passage encourages trying out the Traceroute tool at www.traceroute.org to see the end-to-end route of packets on the Internet.

### circuit switching

Circuit-Switched Networks:
1. **Connection Establishment**: A dedicated end-to-end connection (circuit) is established before communication begins.
2. **Resource Reservation**: The network reserves a constant transmission rate across its links for the duration of the connection.
3. **Guaranteed Rate**: The sender can transfer data at a guaranteed constant rate, as bandwidth is allocated specifically for that connection.
4. **Example Structure**: Circuit switches are interconnected by links, each having multiple circuits (e.g., four circuits per link).
5. **Capacity Allocation**: Each circuit gets a fraction of the link's total transmission capacity (e.g., if a link is 1 Mbps, each circuit may get 250 kbps).

Packet-Switched Networks:
1. **No Connection Establishment**: Packets are sent without establishing a dedicated connection or reserving resources.
2. **Dynamic Resource Usage**: Link resources are used on a first-come, first-served basis, with no guarantees for bandwidth.
3. **Congestion Handling**: If links are congested, packets may have to wait in a buffer, leading to variable delays.
4. **Best Effort Delivery**: The network makes a best effort to deliver packets in a timely manner but does not guarantee delivery speed or reliability.

- A circuit in a link is implemented with either **frequency-division multiplexing (FDM) or time-division multiplexing (TDM)**
- FDM
	- the link dedicates a frequency band to each connection for the duration of the connection.
	- The width of the band is called, not surprisingly, the bandwidth. 
- TDM
	-  time is divided into frames of fixed duration, and each frame is divided into a fixed number of time slots.
	- one time slot for every frame in the connection
	- slots are dedicated for the sole use of that connection
- problem
	- circuit switching is waste-ful because the dedicated circuits are idle during silent periods.
	- the idle network resources (frequency bands or time slots in the links along the connection’s route) cannot be used by other ongoing connections

#### network of networks

Access ISPs:
1. **Connectivity Options**: Access ISPs connect end systems (e.g., PCs, smartphones) using various technologies like DSL, cable, FTTH, Wi-Fi, and cellular.
2. **Types of Access ISPs**: Access ISPs can include universities and companies, not just telcos or cable companies.

Network Structure Evolution:
1. **Network of Networks**: The Internet is described as a "network of networks," requiring interconnection among access ISPs.
2. **Direct Connections**: A naive approach would involve each access ISP connecting directly to every other ISP, which is impractical due to cost.

Hierarchical Structures:
1. **Network Structure 1**: Introduces a global transit ISP that interconnects all access ISPs but is costly and only one provider exists.
2. **Network Structure 2**: Multiple global transit ISPs emerge, allowing access ISPs to choose among providers based on pricing and services.
3. **Network Structure 3**: Incorporates regional ISPs that connect access ISPs to tier-1 ISPs, forming a multi-tier hierarchy.

Points of Presence (PoPs) and Multi-Homing:
1. **PoPs**: Groups of routers where customer ISPs connect to provider ISPs, enhancing connectivity.
2. **Multi-Homing**: ISPs can connect to multiple providers to maintain service during provider failures.

Peering and Internet Exchange Points (IXPs):
1. **Peering**: ISPs can establish direct connections to exchange traffic without fees (settlement-free), which reduces costs.
2. **IXPs**: Third-party facilities where multiple ISPs can peer, further facilitating efficient data exchange.

Content-Provider Networks:
1. **Network Structure 5**: Introduces content-provider networks (e.g., Google) that operate their own networks to enhance service delivery and reduce costs.
2. **Private Networks**: Content providers connect to lower-tier ISPs directly or via IXPs while also interacting with tier-1 ISPs.

---
1. The Internet is structured as a "network of networks."

2. The text describes five increasingly complex network structures to explain Internet evolution:
   - Network Structure 1: A single global transit ISP connecting all access ISPs.
   - Network Structure 2: Multiple competing global transit ISPs.
   - Network Structure 3: A multi-tier hierarchy with tier-1 ISPs, regional ISPs, and access ISPs.
   - Network Structure 4: Adds points of presence (PoPs), multi-homing, peering, and Internet exchange points (IXPs).
   - Network Structure 5: Incorporates content-provider networks (e.g., Google).

3. Key concepts introduced:
   - Customer-provider relationships between ISPs
   - Tier-1 ISPs (about a dozen exist)
   - Regional and access ISPs
   - Points of Presence (PoPs)
   - Multi-homing (connecting to multiple provider ISPs)
   - Peering (direct connections between ISPs, often settlement-free)
   - Internet Exchange Points (IXPs)

4. Content provider networks (like Google) have their own global private networks, often bypassing upper-tier ISPs.

5. The modern Internet (Network Structure 5) is complex, with diverse ISPs spanning different geographic areas and hierarchical levels.

6. End systems connect to the Internet through access ISPs, which can use various technologies (DSL, cable, FTTH, Wi-Fi, cellular).

7. Access ISPs can be traditional telcos, cable companies, universities, or other organizations.

![[Pasted image 20240921094013.png]]

---
## delay, loss and throughput

Packet Journey:
- **Path**: A packet travels from a source host through routers to a destination host.

Types of Delays:
1. **Nodal Processing Delay**:
    - Time taken for a router or host to process the packet header and make routing decisions.
2. **Queuing Delay**:
    - Time a packet spends waiting in a queue at a router or switch before being transmitted, affected by network congestion.
3. **Transmission Delay**:
    - Time required to push all the packet's bits onto the link, calculated as the **packet's size divided by the link's transmission rate.**
4. **Propagation Delay**:
    - Time it takes for the packet to travel through the physical medium (like fiber optic or copper cable) from one node to the next, depending on the distance and the medium's propagation speed.

Total Nodal Delay:
- The total delay experienced by a packet at each node is the sum of the above delays.

Impact on Applications:
- Network delays significantly affect the performance of various Internet applications, including search engines, web browsing, email, maps, instant messaging, and voice-over-IP.

Importance:
- Understanding these delays is crucial for grasping the dynamics of packet switching and overall computer networks.
#### Processing Delay:
1. **Definition**: The time taken by a router to examine a packet's header and decide its next destination.
2. **Components**:
    - **Header Examination**: Analyzing the packet's header for routing information.
    - **Error Checking**: Verifying the packet for bit-level errors that may have occurred during transmission.
3. **Duration**: Processing delays in high-speed routers are typically very short, often on the order of microseconds or less.
4. **Next Steps**: After processing, the router queues the packet for transmission to the next hop (e.g., router B).

Importance:
- Understanding processing delays helps in analyzing overall network performance and router efficiency.
#### Queuing Delay:
1. **Definition**: The time a packet spends waiting in a queue before it can be transmitted onto the next link.
2. **Factors Influencing Delay**:
    - **Queue Length**: The number of packets already waiting to be transmitted affects the delay. If the queue is empty and no other packets are being transmitted, the delay is zero.
    - **Traffic Load**: Heavy traffic with many packets in the queue results in longer queuing delays.
3. **Duration**: Queuing delays can vary widely, typically ranging from microseconds to milliseconds, depending on network conditions.

Importance:
- Understanding queuing delays is crucial for evaluating network performance and managing traffic effectively.

#### Transmission Delay:
1. **Definition**: The time required to transmit all bits of a packet onto the link.
2. **Calculation**: Transmission delay is calculated as: Transmission Delay= `L/R`
    - **L**: Length of the packet in bits.
    - **R**: Transmission rate of the link in bits per second (e.g., R = 10 Mbps for a 10 Mbps Ethernet link).
3. **Transmission Order**: In a first-come-first-served manner, a packet can only be transmitted after all preceding packets in the queue have been sent.
4. **Duration**: Transmission delays generally range from microseconds to milliseconds, depending on the packet size and link rate.

Importance:
- Understanding transmission delays is essential for evaluating overall network performance and optimizing data transfer.

#### Propagation Delay:
1. **Definition**: The time it takes for a bit to travel from the sender (router A) to the receiver (router B) after being transmitted onto the link.
2. **Calculation**: Propagation delay is calculated as: Propagation Delay= `d/s`
    - **d**: Distance between the two routers.
    - **s**: Propagation speed of the link (typically between 2×10^8 to 3×10^8 meters/second, close to the speed of light).
3. **Transmission Process**: Once the last bit of the packet propagates to router B, all bits are stored and router B proceeds with forwarding the packet.
4. **Duration**: In wide-area networks, propagation delays typically range from milliseconds.

Importance:
- Understanding propagation delays is crucial for analyzing the overall time it takes for data to travel across a network, impacting applications sensitive to latency.

#### end to end delay

![[Pasted image 20240921095124.png]]


## protocol layering

### Network Protocols and Layers:
1. **Layered Architecture**: Network protocols are organized in layers, where each layer offers specific services to the layer above and relies on the services of the layer below. This structure helps organize network design and protocol functionality.

2. **Service Model**: Each layer provides services through:
   - Actions performed within that layer.
   - Utilizing services from the layer directly beneath it.

3. **Examples**:
   - A transport layer might ensure reliable message delivery by detecting and retransmitting lost messages, relying on the underlying layer's basic message delivery service.

4. **Implementation**:
   - Protocols can be implemented in software (e.g., application-layer protocols like HTTP and SMTP) or hardware (e.g., physical layer and data link layer protocols in network interface cards).
   - The network layer often employs a combination of both.

5. **Distributed Protocols**: The functions of a protocol layer are distributed among end systems, packet switches, and other network components, meaning that each part of the network may implement aspects of the same protocol.

### Advantages of Layering:
- **Conceptual Clarity**: Layering provides a structured way to discuss system components, facilitating understanding and communication about network functions.
- **Modularity**: It allows for easier updates and maintenance of individual components without affecting the entire system.

### Potential Drawbacks:
- **Redundancy**: Layers might duplicate functionality, such as having error recovery in both the data link layer and transport layer.
- **Cross-Layer Dependencies**: Functionality at one layer may require information from another layer, violating the intended separation.

### Internet Protocol Stack:
- The Internet protocol stack consists of five layers:
  1. **Physical Layer**: Deals with the transmission of raw bits over a physical medium.
  2. **Link Layer**: Handles communication over a specific link, providing data framing and error detection.
  3. **Network Layer**: Responsible for routing packets across networks.
  4. **Transport Layer**: Ensures reliable end-to-end communication and data integrity.
  5. **Application Layer**: Interfaces with end-user applications and provides application-specific communication protocols.

![[Pasted image 20240921095457.png]]

Here's an overview of how data is transported through the various layers of the Internet protocol stack, detailing the actions taken at each layer:

### Overview of Data Transport Through Layers
1. **Application Layer**:
   - **Function**: This is where network applications and their protocols (e.g., HTTP, SMTP, FTP, DNS) operate.
   - **Data Handling**: When an application needs to send a message, it creates an application-layer message. This message is then passed to the transport layer for further processing.

2. **Transport Layer**:
   - **Function**: Responsible for transporting application-layer messages between application endpoints.
   - **Protocols**: There are two main protocols here: TCP (connection-oriented) and UDP (connectionless).
     - **TCP**: Provides reliable delivery, flow control, segmentation of long messages, and congestion control. The application message is broken into smaller segments.
     - **UDP**: Offers a simpler, no-frills service without reliability or flow control.
   - **Data Handling**: The transport layer adds headers to the application message, creating a segment (TCP/UDP packet), which includes the source and destination ports.

3. **Network Layer**:
   - **Function**: Responsible for routing packets (called datagrams) from the source host to the destination host across multiple networks.
   - **Protocols**: Primarily uses the IP protocol, which defines the format of datagrams and handles addressing and routing.
   - **Data Handling**: The network layer receives the transport layer segment, adds its own header (including source and destination IP addresses), and packages it into a datagram.

4. **Link Layer**:
   - **Function**: Responsible for transferring datagrams between directly connected nodes (e.g., from one router to the next).
   - **Protocols**: Includes various protocols like Ethernet, WiFi, and others, which may provide reliable delivery over a single link.
   - **Data Handling**: The link layer receives the datagram from the network layer, adds a link-layer header (and possibly a trailer for error detection), creating a frame.

5. **Physical Layer**:
   - **Function**: Concerned with the physical transmission of bits over the communication medium (e.g., electrical signals, light pulses).
   - **Data Handling**: The physical layer takes the frames from the link layer and transmits the individual bits over the network medium to the next node.

### Data Flow Summary
- **From Application to Transport**: The application generates a message and hands it to the transport layer, which segments it if necessary.
- **From Transport to Network**: The transport layer adds a header to create a segment, passing it to the network layer.
- **From Network to Link**: The network layer wraps the segment in a datagram, adding its own header, and sends it to the link layer.
- **From Link to Physical**: The link layer encapsulates the datagram in a frame and sends it to the physical layer, which transmits the bits across the medium.

### Routing Through Layers
- At each node (host or router), this process repeats: the physical layer receives bits, the link layer decodes the frame and extracts the datagram, the network layer processes the datagram and determines the next hop, and so on, until the data reaches its final destination.