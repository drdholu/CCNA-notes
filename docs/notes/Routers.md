Switches are good in helping connect devices that are present in a given network range (For ex - 10.1.1.0 - 10.1.1.255), but what if we want to send frames to a device which is in another network range?

You'd probably think that we can do this task simply by connecting two switches that belong to two different network ranges. But this is wrong.
Let us see why.

## Same network range devices

Initially, the source needs to learn the L2 address (MAC address) of the destination, so it sends a broadcast packet to the switch, since this is a broadcast packet, all the devices connected receive this packet. The device which was actually meant to receive this, accepts the packet and resends the confirmation packet along with its L2 address (MAC address).

> Who assigns MAC addresses? The Institute of Electrical and Electronics Engineers (**IEEE**) is the primary organization responsible for assigning MAC addresses

Note that this broadcast packet contains L1 and L2 data. The L2 contains something called **ARP Packet Source** which contains the L3 addresses of the source and destination. 

**This broadcast packet is formally called the ARP packet.**

![Pasted image 20240528142534.png](./images/Pasted%20image%2020240528142534.png)

> ARP entries are of *2 types*, **Dynamic** and **Static**
> - Dynamic: automatically maintained and generated
> - Static: admins manually create these entries

## Different network range devices

Now this all works when the devices are in the same **network range**. Once we try to send frames to a device in another network range, the switch goes haywire.

Lets we try to connect a 10.1.1.2 device to a random device in the 23.227.38.0 - 23.227.38.255 range.

The 1.2 devices gives up immediately instead of trying to find out the MAC address of the destination device (belonging to the other network range).

If we take a closer loop at the ARP packet, the ARP Packet source which should contain the source and destination IP addr. actually contains **the source and the gateway IP address.** (here gateway is 10.1.1.1)

![Pasted image 20240528143952.png](./images/Pasted%20image%2020240528143952.png)

Example of a device having which has a gateway address set

![Pasted image 20240528144114.png](./images/Pasted%20image%2020240528144114.png)

What is the gateway IP address? This address is what is actually used by a router to allow us to connect with devices out of our network range.

Which is why we require routers to connect to devices out of our network range.

# Formal definition of routers

Routers are a medium to connect different networks to allow transmission of data between devices that belong to different network (network ranges).

## What happens when routers get a ARP packet?

Recall how the 10.1.1.2 sends an ARP packet to find out the MAC address of a device it wants to connect in 23.227.38.0 - 23.227.38.255 range.

Now that the router has received this ARP packet, it further will send another ARP packet as the router itself doesn't have the MAC address of the destination device. Here it sends it the ARP packet to a switch which falls in the network range of the destination device.

Now once this ARP packet reaches the switch, the switch then again sends an ARP packet to all the devices connected to it. Once it the destination device receives the ARP packet, it sends back a confirmation packet back to through the same route towards the source.

Once the source receives this, it keeps a record of it for future communications.

