## 5.2 Routing algorithms

- goal of a routing algo is to provide a *good* path that provides the least cost.
	- good: least cost, fastest path, least congested path
- Graph abstraction
	- Graph G = (N, E)
	- N: set of routers
	- E: set of links
	- Each router that is **directly connected** may have a **set cost**, but routers that *aren't directly connected* have a cost of *infinity*. 
	- Cost maybe 1 or defined by router operator or based on other conditions (congestion, bandwidth etc)


![[Pasted image 20241125121216.png]]
### Link State Algorithm
- all link costs are already known and act as an input to the LS algorithm
- a type of LS algo is **Dijkstra algo**
- this computes the least-cost path from *one node to all other nodes in the network*
- kth iteration -> least cost paths of K nodes

![[Pasted image 20241125152320.png]]
![[Pasted image 20241125155615.png]]

#### oscillations


### distance vector algorithm
- decentralized, iterative, asynchronous and iterative type of algorithm
	- decentralized: doesn't have information of all the nodes, only info of the directly connected neighbors
	- distributed: each node receives some info from its neighbours
	- async: all nodes need to operate in lockstep with each other
	- iterative: process continues until no more information is exchanged between neighbours.
	- $min_v$ in the equation is taken over all of x’s neighbors
- in this algo each node x consists of 
	- c(x,v): cost for x -> v
	- D_x (distance vector of x): x's estimate of its cost to its neighbors
	- D_v (distance vector of neighbors)
- least cost for a node is calculated using the **Bellman ford equation** for a node![[Pasted image 20241125180759.png]]

> cost of "me" to "my neighbor" + cost of "my neighbor" to destination

- Example of a topology![[Pasted image 20241125180849.png]]
- New computations for node b using BF equation![[Pasted image 20241125182152.png]]
- DV-like algorithms are used in many routing protocols in practice, including the Internet’s RIP and BGP, ISO IDRP, Novell IPX, and the original ARPAnet.
- "Good news travels fast" & "Bad news travels slow"
	- **Good news**: a *decrease* in the cost of one of the links
	- **Bad news**: an *increase* in the cost of one of the links
	- The increase in cost can create a **routing loop** as the DV algo doesn't update the nodes properly.
	- In the (b) diagram, the change from 4 -> 60 would take a lot of cycles until z knows that y -> x cost is 60. Whereas the decrease in cost, 4 -> 1, takes 3 cycles (t0, t1, t2) ![[Pasted image 20241125190011.png]]


![[Pasted image 20241125190701.png]]

> **Black holing** in distance vector algorithms, particularly in the context of routing protocols like AODV (Ad Hoc On-Demand Distance Vector), refers to a type of denial-of-service (DoS) attack where a malicious node falsely claims to have a valid route to a destination. This node then drops all packets sent through it, effectively "black holing" the data and preventing it from reaching its intended destination.
#### bellman ford example
all tables initially![[Pasted image 20241015105045.png]]

updating table N1![[Pasted image 20241015105540.png]]

updating table n5![[Pasted image 20241015111056.png]]
![[Pasted image 20241015111235.png]]

updating table n2![[Pasted image 20241015113301.png]]

---

## 5.3 intra-as routing in the internet: OSPF

All routers being homogenous (in terms of their routing algo) is simplistic for two reasons
- **Scale**
	- the global network has *billions of devices*, each device couldn't possibly contain a record for all billion devices.
	- the complexity of making the algo work would be extremely hard.
- **Autonomy**
	- each ISP should be allowed to use their own algorithms within their network
	- and at the same time connect with the outside network

These problems can be solved using **Autonomous systems**, a group of routers under the *same administrative control*.

- **Intra** AS: routing among the same network/AS
	- **gateway router** at the edge of an AS helps with the connection of different AS'es.
	- common protocols
		- RIP (DV algo, now used for Inter)
		- OSPF (link state algo)
		- EIGRP (DV algo)
- **Inter** AS: routing among different AS'es
	- gateways perform inter AS routing
	- common protocols
		- Border Gateway Protocol (BGP)

### Open Shortest Path First

- "open": specifications are publicly available.
- link state protocol.
- **link-state advertisement** packets are flooded to other routers in the AS.
- the state is broadcasted *every 30 minutes*.
- the advertisement is sent along with the **OSPF message** which is transferred by the *IP* where the header for *upper layer protocol* is set to **port 89**.
- each router locally runs the *Dijkstra algorithm* to determine the shortest path tree to all subnets
- Provides security
	- **Simple**
		- configured password is attached to the OSPF packet in plain text
		- not completely secure
	- **MD5**
		- hash(preconfig password + OSPF content)
		- hash is added to OSPF packet
		- destination does a hash(packet) and compare w/ hash value that the packet carries.
- Allows multiple paths to be taken if they have same costs.
- Integrated support for multicast (MOSPF - multicast OSPF) Zand anycast routing.
- Supports hierarchy within an AS.
	- example of **two level hierarchy**
		- consists of multiple areas. each area has its own **border router**.
		- these areas are connected via a **backbone**. the border router is part of this backbone.
		- areas have info only about their area, communicate w/ other areas through backbone.
		- in the backbone, there is a **boundary router** which helps in connecting with other AS'es.
- Example of hierarchical OSPF ![[Pasted image 20241125220113.png]]