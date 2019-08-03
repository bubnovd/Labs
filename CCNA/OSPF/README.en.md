[<img width=30 height=20 src="../../images/en.png">](README.en.md)  [<img width=30 height=20 src="../../images/ru.png">](README.md)
# OSPF
File `CCNA_OSPF.zip` contains network topology for import to EVE-NG.
Used devices:
* Router - `L3-ADVENTERPRISEK9-M-15.4-2T`
* Switch - `L2-ADVENTERPRISEK9-M-15.2-20150703`

*Because of limits of Cisco IOU images all network interfaces have only ten-megabites Ethernet. In original labs in pt. 6 need to set reference-bandwidth in 10 Gbps. But in this labs set to 10 Mbps*
Lab Answers with [Cisco](Cisco.Answer_key.en.md)

![Lab1](https://github.com/devi1/Labs/blob/master/CCNA/OSPF/lab1.png) 
## OSPF Basic Configuration
1. Enable a loopback interface on each router. Use the IP address 192.168.0.x/32, where ‘x’ is the router number. For example 192.168.0.3/32 on R3
2. Enable single area OSPF on every router. Ensure all networks except 203.0.113.0/24 are advertised
3. What do you expect the OSPF Router ID to be on R1? Verify this
4. Verify the routers have formed adjacencies with each other
5. Verify all 10.x.x.x networks and loopbacks are in the router’s routing tables
6. Set the reference bandwidth so that a 1000 Mbps interface will have a cost of 1
7. What will the OSPF cost be on the FastEthernet links? Verify this
8. What effect does this have on the cost to the 10.1.2.0/24 network from R1?

## OSPF Cost
9. There are two possible paths which R1 could use to reach the 10.1.2.0/24 network – either through R2 or R5. Which route is in the routing table?
10. Change this so that traffic from R1 to 10.1.2.0/24 will be load balanced via both R2 and R5
11. Verify that traffic to the 10.1.2.0/24 network from R1 is load balanced via both R2 and R5

## Default Route Injection
12. Ensure that all routers have a route to the 203.0.113.0/24 network. Internal routes must not be advertised to the Service Provider at 203.0.113.2
13. Verify that all routers have a path to the 203.0.113.0/24 network
14. Configure a default static route on R4 to the Internet via the service provider at 203.0.113.2
15. Ensure that all other routers learn via OSPF how to reach the Internet
16. Verify all routers have a route to the Internet

## Multi-Area OSPF
17. Convert the network to use multi-area OSPF. R3 and R4 should be backbone routers, R1 a normal router in Area 1, and R2 and R5 ABRs as shown in the diagram below ![Areas](https://github.com/devi1/Labs/blob/master/CCNA/OSPF/areas.png) 
18. Verify the router’s interfaces are in the correct areas
19. Verify the routers have formed adjacencies with each other
20. What change do you expect to see on R1’s routing table? Verify this (give the routing table a few seconds to converge)
21. Do you see less routes in R1’s routing table? Why or why not?
22. Configure summary routes on the Area Border Routers for the 10.0.0.0/16 and 10.1.0.0/16 networks
23. Verify R1 now sees a single summary route for 10.1.0.0/16 rather than individual routes for the 10.1.x.x networks
24. Verify R1 is receiving a summary route for the 10.1.0.0/16 network from both R2 and R5
25. R1 is routing traffic to 10.1.0.0/16 via R2 only. Why is it not load balancing the traffic through both R2 and R5?