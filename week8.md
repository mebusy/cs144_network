
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











