---
title: "Segments, Packets and Frames"
date: 2024-03-19T13:03:52+05:30
publishdate: 2024-03-19
lastmod: 2024-03-19
draft: true
tags: [Networking]
categories: [Linux]
---
##### Protocol Data Unit (PDU)
The term PDU is used to refer to the packets in different layers of the OSI model. 
Thus PDU gives an abstract idea of the data packets. The PDU has a different meaning in different layers 

1. The PDU of Transport Layer is called as a Segment.
2. The PDU of Network Layer is called as a Packet.
3. The PDU of the Data-Link Layer is called Frames.

The Application Layer can give any amount of data to the underlying layers, but it is not possible to send all the data directly. 
Thus the TCP breaks the data given by the application layer into MSS (Maximum Segment Size) that the network can handle
so that further fragmentation doesn't happen in the router.
And the TCP is responsible for acknowledgment when segments are delivered. 

##### Segment
The data from the application layer is broken down into smaller parts as per the MSS of the network, and a TCP header is added to
the smaller parts. The size of the header could be between 20 bytes and 60 bytes. (Usually it is 20B and 40B is optional)

The header of TCP includes
```bash
1. Source Port
2. Destination Port
3. Flag bits (like DF, MF, etc)
4. Sequence Number of the Segments
5. Checksum
6. Options Field 
```
Source and Destination port are required because it tells where the PDU has to be delivered in the receiver host. 
The checksum field of the TCP is calculated by taking into account the TCP header, data and IP pseudo-header. 
The Checksum ensures that correct data is sent and received. Thus, after all these processing the broken data packets are called Segments. 

##### Packets
The segments received from the Transport layer are further processed to form the Packets. The IP packet has a header of varying sizes
from 20B to 60B. But usually, it is 20B. The IP header has many fields namely:-
```bash
1. Source IP Address
2. Destination IP Address
3. TTL(time to live)
4. Identification
5. Protocol type 
6. Version (version of protocol)
7. Options
```
Now let’s understand the concept, the IP body contains the Segment received from the Transport Layer without any of the modifications. 
To the IP body, the IP header is added which has the fields as mentioned above. The IP headers are continuously modified as the packets 
in the networks because TTL keeps on changing with each hop. 
Thus the IP header along with the body (which contains the segment from the Transport layer) makes the Packet, also called IP Packet.
In each network, there is a maximum size of the data to be transmitted called the MTU (Maximum Transmitted Unit).
Packets can often be larger than the maximum size, so each packet is also divided into smaller pieces of data called fragments.
This layer is also responsible for fragmentation if required. This fragmentation is done at the Routers. 
When the network layer receives a packet, it checks the MTU of the packet. If the packet length is bigger than the MTU,
the network layer checks the Don’t Fragment (DF) flag associated with the packet. If the DF flag is \mathsf{1}, we discard the packet. 
Otherwise, the network layer decides the size of the fragments, create the header, encapsulate the fragments within the header, 
and send them to the next layer:


##### Frames
The Packets received from the Network Layer further processed to form the Frames. Here is the Data link layer the header is added, 
the header consists of the fields.
```bash
1. Source Mac Address
2. Destination Mac Address
3. Data
4. Length
5. Checksum (CRC)
```
The source MAC address is resolved by using the ARP(Address Resolution Protocol). The Source and Destination MAC address would keep 
on modifying as the Frame moves in the network. The Modification of the MAC address is done by the Routers. Data is the segment that 
is received from the network layer. The length is the total MTU(maximum transferable unit) of the network. 
All concepts will be clear with the diagram given below. 

![Concepts](/images/Packets.png)


