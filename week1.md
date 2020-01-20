
# week1 

## 1.2 The 4 Layer Internet Model 

The Internet is made up of end-hosts, links and routers. Data is delivered hop-by-hop over each link in turn. Data is delivered in packets. 

1. Link 
    - The Link layer's job is to carry the data over one link at a time. 
    - Ethernet and wifi are 2 examples of different Link layers. 
2. Network
    - The network lyaer's job is to deliver packets end-to-end across the Internet from the source to destination. 
    - Network layer packets are called datagrams: `|Data|from|to|`.  They consist of some data and a header containing the "To" and "From" addresses --just like we put the "To/From" address on a letter. 
    - The Network hands the datagram to the Link layer , telling it to send the datagram over the first link. In other words, the Link Layer is providing a *service* to the Network Layer: "if you give me a datagram to send, I will transmit it over one link for you". 
    - At the other end of the link is a router. The Link layer of the router accepts the datagram from link, and hands it up to the Network layer inside the router. The network lyaer on the router examines the destination address of the datagram, and is responsible for routing the datagram on hop at a time towards its eventual destination. It does this by sending to the Link layer again, to carry it over the next link.
    - and so on until it reaches the Network layer at the destination. 
    - **The Network layer provides an unreliable datagram delivery service.**
3. Transport
    - The most common Transport Layer is TCP (Transmission Control Protocol). 
    - TCP makes sure that data sent by an application at one end of the Internet is correctly delivered in the right order, to the application at the other end of the Internet. If the Network layer drops some datagrams, TCP will retransmit them, multiple times if need-be. 
    - If an application doesn't need reliable delivery, it can use the much simple UDP (User Datagram Protocol) instead. UDP offers no delivery guarantees. 
4. Application
    - BitTorrent, Skype, www ... 


### The network layer is "special"

We must use the Internet Protocol (IP)

- IP makes a best-effort attempt to deliver our datagrams, but it makes no promises. 
- IP datagrams can get lost , out of order, and can be corrupted. There are no guarantees.

If an application wants a guarantee that its data will be retransmitted when necessary and will be delivered to the applicaiton in order and without corruption then it needs another protocol running on top of IP. 

This is the job of the Transport Layer ...

### Summary

![](imgs/week1_summary_1.png)

![](imgs/week1_summary_2.png)


## 1.3 The IP service model

### The Internet Protocol (IP)

Walking through the service provided by the Internet Protocol.

![](imgs/week1_IP_1.png)

IP datagrams consist of a header and some data. 

When The transport layer has data to send, it hands a Transport Segment to the Network layer below.  The network layer puts the transport segment inside a new IP datagram. 

IP's job is to deliver the datagram to the other end. 

![](imgs/week1_IP_2.png)

But first , the IP datagram has to make it over the first link to the first router. 

So the IP sends the datagram to the Link Layer that puts it inside a Link Frame, such as an Ethernet package and ships it off to the first router. 

![](imgs/week1_IP_3.png)


### The IP Service Model

Property | Behavior
--- | ---
Datagram | Individually routed packets, hop-by-hop routing (just like how letters are routed by the postal service)
Unreliable | Packets might be dropped
Best effort | ... but (drop) only if necessary 
Connectionless | No per-flow state, packets might be mis-sequenced.

### Why is the IP service so simple ?

- Simple, dumb, minimal:  Faster, more streamlined and lower cost to build and maintain. 
- The end-to-end principle: Where possible, implement features in the end hosts. 
- Allows a variety of reliable (or unreliable) services to be built on top.
- Works over any link layer: IP makes very few assumptions about the Link layer below. 

### The IP Service Model (Details)

5 features , understand the scope of the complete IP service.

1. Tries to prevent packets looping forever
    - It is possible for the forwarding table in a router to be wrong, causing a packet to start looping round and round following the same path. 
    - This is most likely to happen when the forwarding tables are changing and they temporarily get into an inconsistent state. 
    - Rather than try to prevent loops from ever happending -- which would take a lot of complexity -- IP uses a very simple mechanism to catch and then delete packets that appear to be stuch in a loop. To do this, IP simply adds a hop-count field in the header of every datagram. It is called the TTL(time to live) field, starts out a number like 128, and then is decremented by every router it passes through. 
2. Will fragment packets if they are too long.
    - Most links have a limit on the size of the packets they can carry. 
        - For example, Ethernet can only carray packets shorter than 1500bytes long. If an application has more than 1500 bytes to send, it has to be broken into 1500 pieces before sending in an IP datagram.
        - Now along the path towards the destination, a 1500 bytes datagram might need to go over a link that can only carry smaller packets, for example 1000 bytes.  
        - The router connecting the 2 links will fragment the datagram into 2 smaller datagrams.
    - IP provides some header fields to help the router fragment the datagram into 2 self-contained IP datagrams, while providing the information the end-host needs to correctly reassemble the data again. 
3. Uses a header checksum to reduce chances of delivering datagram to wrong destination. 
    - IP includes a checksum field in the datagram header to try and make sure datagrams are delivered to the right location. It could be quite a security problem if packets are accidentally and frequently sent to the wrong place because of a mistake by a router along the way. 
4. Allows for new version of IP
    - Currently IPv4  with 32 bit addresses
    - And IPv6 with 128 bit addresses
5. Allows for new options to be added to header. 
    - This is a mixed blessing. On the one hand, it allows new features to be added to the header that turn out to be important, but weren't in the original standard. On the other hand, these fields need processing and so require extra features in the routers along the path, breaking the goal of simple, dumb, minimal forwarding path.
    - In practice, very few options are used or processed by the routers. 


### IPv4 Datagram


- Protocol ID 
    - That tells use what is inside the data filed.
    - Essentially, it allows the destination end-host to demultiplex arriving packets, sending them to the correct code to process the packet.  
    - If the protocol ID has the value "6", then it tells us the data contains a TCP segment, and so we can safely pass the datagram to the TCP code and it will be able to parse the segment correctly. 
    - The Internet Assigned Numbers Authority (IANA) defines over 140 different values of Protocol ID, representing different transport protocols. 
- Version 
    - tells us which version of IP,  currently  the legal values are IPv4 and IPv6.
- Total Packet Length
    - can be up 64k bytes including the header and all the data. 
- TTL 
    - help to prevent packets accidentally looping in the network forever. 
- Packet ID, Flags, Fragment Offset
    - Sometimes a packet is too long for the link it is about to be sent on.  The Packet ID, Flags and Fragment Offset all help routers to fragment IP packets into smaller self-contained packets if need-be. 
- Type of Service
    - which gives a hint to routers about how important this packet is. 
- Header Length (, (OPTIONS), (PAD) )
    - tells us how big the header is -- some headers have optional extra fields to carry extra information. 
- Checksum 
    - a checksum is calculated over the whole header so just in case the header is corrupted, we are not likely to deliver a packet to the wrong destination by mistake.

## 1.4 Life of a Packet 

### TCP Byte Stream 

- 3-way handshake 
    - SYN, SYN/ACK, ACK
    - ![](imgs/week1_3way_handshake.png)
- Opening a TCP connection to a program need 2 addresses. 
    1. IP address,  the addresses the network layer uses.
    2. TCP port, tells the computer software which application to deliver data to.
    - ![](imgs/week1_tcp_ipport.png)

### Inside Each Hop

How does a router make decision ?  It use something called *forwarding table*. 
