
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

### IP is the "thin waist"


## 1.3 The IP service model


