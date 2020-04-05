
# Week6 Routing

## The Basics

- Approaches 
    - Flooding
    - Source routing
        - `...|B|R₃R₂R₁`
        - A knows the topology, it knows the order in which it wants the routers to be visited. 
    - Forwarding table
    - Spanning tree
        - When populating the forwarding tables with entries, it's often the goal to create what we call the spanning tree. 
        - The spanning tree is used in order to create the routing entries so that we can populate the forwarding tables and therefore route paths along that spanning tree.
        - ![](imgs/cs144_6.0_spanning_tree_ex.png)
- Metrics
    - When calculating the spanning tree, we need to know what our objective is, or what our metrics of success are. How do we know, amongst all of the possible spanning trees, which one we're going to pick ? 
    - Choices 
        - Min distance
        - Min hop-count
        - Min delay
        - Max throughput
        - Least-loaded path
        - Most reliable path
        - Lowest cost path
        - Most secure path
        - ... 
- Shortest path spanning trees
- Other type of routing 
    - Multipath
        - The downside of the shortest path spanning tree is that some links can become very popular, and could become congested. 
        - So it means that we might need to keep adapting the algorithm. An alternative would be, instead of adapting the algorithm, from the beginnings spread all of the traffic over all of the links. 
    - Multicast
        - So far, we have assumed that all packets are going to a single destination, something we call unicast. 
        - In some applications, an end host might want to send packets to a set of hosts. 


## Bellman Ford 


