> Note that the things written here about the OSI model are also applicable in the TCP/IP model.

## Application Layer

The application layer serves as the interface or portal for applications to communicate over networks. It provides services such as email, file transfer, and web browsing.

## Presentation Layer

The presentation layer is responsible for making data presentable by handling data formats and encryption. It converts data into standardized formats such as PDF or HTML and ensures data security through encryption protocols like SSL.

## Session Layer

The session layer facilitates communication between the source and destination while other layers perform their functions. It manages sessions between applications and provides services such as establishing, maintaining, and terminating connections.

## Transport Layer

The transport layer determines how data is delivered from the source to the destination. It selects appropriate protocols, such as TCP for reliable transmission or UDP for faster transmission. TCP ensures reliable delivery by waiting for acknowledgment, while UDP prioritizes speed over reliability.

### TCP vs UDP

- TCP: Reliable transmission, waits for acknowledgment.
- UDP: Faster transmission, does not wait for acknowledgment.

Example: YouTube uses TCP for sending web page data and switches to UDP for streaming videos to prioritize real-time delivery.

# Ports

Ports are used at the transport layer to facilitate communication between devices. They allow the destination to provide different services on different ports.

## Purpose of Ports

Ports enable multiple services to run on a single device by assigning unique port numbers to each service. For example, HTTPS traffic typically uses port 443, FTP uses port 21, and SSH uses port 22.

## Source Port Assignment

When a device communicates with another device, it is assigned a source port to allow the destination to communicate back. This assigned port, known as an ephemeral port, is chosen from the range of available ports (0-65,535).

### Ephemeral Port

- An ephemeral port is a randomly assigned port used by the sender for communication.
- It allows the destination to reply to the sender's communication.

>Ports with numbers `0-1023` are called system or **well-known** ports; ports with numbers `1024-49151` are called user or **registered** ports, and ports with numbers `49152-`65535` are called dynamic, private or **ephemeral** ports.  
>Registered port numbers are currently assigned by the... IANA... and were assigned by... ICANN... before March 21, 2001, and were assigned by the... USC/ISI... before 1998.

Example Communication:
`your_ip_addr:ephemeral_port --> known_ip_addr:443` (443 for HTTPS)

Read more about ephemeral ports [here](https://unix.stackexchange.com/questions/65475/ephemeral-port-what-is-it-and-what-does-it-do).