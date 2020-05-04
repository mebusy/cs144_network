
# Week8 : Security

## 8.1 Introduction

### How can communication be compromised ?

1. Eavesdrop -- passively "sniff" and record network data ( or metadata ). For example:
    - Passively tap an electrical or optical cable
    - Listen to Wifi ( as we did in class, using Wireshark )
        - because the packets are broadcast for everyone to hear.
    - Compromise a router to duplicate and forward data
2. Modify, delete, instert -- Actively tamper with our data by:
    - Changing contents of packets
    - Redirect packets to anothe server
    - Take over control of an end-host
3. Prevent communication -- Usually called "denial of service".

### What we want 

1. Secrecy/confidentiality: No one can listen-in to our communication. We will study encryption.
2. Integrity -- Our message are not altered in transit. We will study message authentication codes (MACs).
3. Authentication -- Confirm the identity of the other party. We will study digital signatures and certificates.
4. Uninterrupted communication -- We don't want someone to prevent us from communicating. 


### Types of attack 

1. Eavesdropping
2. Redirecting Ethernet, IP and DNS traffic
3. Hijacking a running TCP connection.
4. Denial of service

## 8.2 Layer 2 Attacks

### Common types of attack at Layer 2

1. Eavesdropping with an interface in promiscuous mode
    - quite easy if we put our network interface into promiscuous mode
    - in promiscuous mode, your network interface will capture all of the packets passing by, not just those addressed to your Ethernet address. 
    - Computers allow this mode of operation so that they can act as an Ethernet switch
    - for example, Linux comes with Ethernet switching code builtin. When wireshark runs it first puts your interface into promiscuous mode so it can see all the packets. 
    - This is particularly easy in a WiFi network and early Ethernet networks where packets were broadcast onto a single shared link.
    - Doesn't work so well with modern Ethernet networks that use Ethernet switches, because packets are usually private to the links between the source and the destination. 

2. Force packets to be broadcast -- then use methed(1)
    - force packets to be broadcast in any Ethernet network by overflowing the forwarding tables.  We do this by preventing the switch from learning addresses correctly. 
    - Once we have forced then to be broadcast, we can then eavesdrop using Wireshark.

3. Masquerade as DHCP or ARP server -- redirect packets to a different end-host

---

- How can the attacker persaude the swith to broadcast packets ?
    - It can keep filling up the forwarding table with other addresses. 
    - what can attacker can do is keep sending -- at very high rate -- packets with new Ethernet addresses. The switches will learn these addresses, displacing entries already in the switches, the table will keep evicting the entries for Alice and Bob. All the packets will then be be broadcast, and will be seen by the attacker.
    - ![](imgs/cs144_attack_swither.png)
    - This is called a MAC overflow attack.

### DHCP and DNS Masquerade

The attacker is going to try and persuade you to use a rogue DHCP server instead. 

Recall: When your computer is boots or first attaches to the network, your computer sends out a sequence of broadcast discovery packets to find the DHCP server. which is usually hosted on the nearest router.

After your computer has found the DHCP server, it sends a request, asking to be allocated an IP address on the local network, along with the address of the default router and the addresses of the DNS servers it should use. 

If the rogue DHCP server can respond faster than the legitimate server, it can respond faster than the legitimate server, it can respond to Alice first, giving here whatever configuration information it wants. 


For example, the attacker can give Alice a bad router address, so he sends traffic to the attacker instead of the router.  This makes it easy for the Attacker to set up a main in the middle attack.

![](imgs/cs144_8.1_att_dhcp.png)

A second way is for the attacker to give Alice the IP address of a remote rogue DNS server. When Alice looks up IP addresses in future -- for example, next time she visits google.com -- the rogue DNS server can return the IP address of a different server, and intercept Alice's traffic.

![](imgs/cs144_8.1_att_dns.png)


### ARP Masquerade

Finally, the attacker can set up a rogue ARP server. When Alice is sending packets to a local host, or via the router, she will first send an ARP request to find out the Ethernet address of the next hop. 

First she sends a broadcast ARP request packet to the ARP server, which replies with the legitimate Ethernet address she is looking for.

But if the attacker  sets up a rogue ARP server that responds faster than the legitimate ARP server, the attacker can give Alice the wrong information. If the attacker replies with the ethernet address of a rogue server in the local network, then all of Alice's traffic will be sent to the rogue server. This is an easy way to setup a man in the middle attack. 

## 8.2 Demo Attack

- Alice 10.0.0.1
- Bob   10.0.0.2
- Evil  10.0.0.3

```bash
# alice is ping Bob
ping 10.0.0.2

# evil can see any traffic
tcpdump -n host 10.0.0.1

# now evel run a script to 
# filling up the forward table 
# Alice's packet start broadcast...
```

---

DHCP attack ...


## 8.3 Layer 3 Attacks












