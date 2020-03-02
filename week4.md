
# 4  Congestion Control

You can imagine that if senders were to send too many packets into the network, ther are going to overwhelm the network. The buffers of routers are going to fill up, the links are going to overflow, and we're going to start dropping packets. 

Congestion control is about preventing the senders from overwhelming the network. 

In this unit, we're going to talk about TCP and how TCP controls congestion. 

### Outline

- What is congestion control ?
- Basic approaches 
    - in the network
    - from the end host
- TCP Congestion Control
    - TCP Tahoe
    - TCP Reno
    - TCP RTT Estimation
    - Performance in practice


## 4.1 Basic


### Max-min fairness: Single Link

```
A(0.5) \
B(1)   ---- Router (1)----
C(0.2) /
```

What would be the fair share?

C is the minimum. So we're going to start by allocating the minimum. 

The fair share of A,B,C is 1/3. And C wants less than its fair share.  So we're going to allocate to it 0.2. That's going to leave 0.8 on Router's link. And the fair share of the other two would be 0.4. A wants more than that so it's going to be curtailed to 0.4.  B also wants more , so it's gonna get 0.4 as well.

```
A(0.5) \
B(1)   ---- Router (A/0.4, B/0.4, C/0.2)---- 
C(0.2) /
```

### Goals for congestion control 

1. High throughput:  Keep links busy and flows fast
2. Max-min fairness
3. Respond quickly to changing network conditions
4. Distributed control

### TCP Congestion Control

TCP implements congestion control at the end-host

- Reacts to events observable at the end host(e.g. packet loss)
- Exploits TCP's sliding window used for flow control
- Tries to figure out how many packets it can safely have outstanding in the network at any time.
- Varies window size according to AIMD.


- Sliding Windows


```
           |<-------- Window Size ------>|
|||||||||||||||||||||||||||||||||||||||||||||||||||||||
           |                 |           |
Data ACK'd |Outstanding Data |  Data OK  | Data not OK
           |sent,but un-ack'd|  to Send  | to send yet
```


TCP varies the number of outstanding packets in the network by varying the window size:

Windwos size = min{ Advertised window (Receive) , Congestion Window (Transmitter) }

Congestion window (CWND) is calculated at the source.

How do we decide the value for cwnd ?

The scheme that we're going to use is AIMD( additive increase, multiplicative decrease ).

1. If packet received OK: W ← W + 1/W
    - every time a packet is received by the sender, it's going to increase the CWND windows size by 1/W.
    - what this means is that every time a packets is received and acknowledged , then the sender is going to increase its windows 
2. If a packet is dropped:  W ← W/2


## 4.2 Dynamics of a single AIMD flow

![](imgs/cs144_sawteeth.png)

![](imgs/cs144_AIMD.png )


## 4.4 Multiple Flows





