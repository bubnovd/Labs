[<img width=30 height=20 src="../../images/en.png">](README.en.md)  [<img width=30 height=20 src="../../images/ru.png">](README.md)
# Switching
File `CCNA_Switching.zip` contain network topology for import to EVE-NG.
Used devices:
- CCNA_Switching - Cisco:
  - Router - `L3-ADVENTERPRISEK9-M-15.4-2T`
  - Switch - `L2-ADVENTERPRISEK9-M-15.2-20150703`

*Because of limits of Cisco IOU images all network interfaces have only ten-megabites Ethernet*


![Lab1](https://github.com/devi1/Labs/blob/master/CCNA/Switching/lab1.png) 
## Link Aggregation
### LACP EtherChannel Configuration
1. The access layer switches SW3 and Sw4 both have two Ethernet uplinks. How much total bandwidth is available between the PCs attached to SW3 and the PCs attached to SW4?
2. Convert the existing uplinks from SW3 to SW1 and SW2 to LACP EtherChannel. Configure descriptions on the port channel interfaces to help avoid confusion later
3. Verify the EtherChannels come up *In my installation LACP is not working. Perhaps, it's GCP specificity. But other EtherChannels are works well*

### PAgP EtherChannel Configuration
4. Convert the existing uplinks from SW4 to SW1 and SW2 to PAgP EtherChannel
5. Verify the EtherChannels come up

### Default Route Injection
6. Convert the existing uplinks between SW1 and SW2 to static EtherChannel
7. Verify the EtherChannels come up
8. How much total bandwidth is available between the PCs attached to SW3 and the PCs attached to SW4 now?

## Multi-Area Switching
17. Convert the network to use multi-area Switching. R3 and R4 should be backbone routers, R1 a normal router in Area 1, and R2 and R5 ABRs as shown in the diagram below ![Areas](https://github.com/devi1/Labs/blob/master/CCNA/Switching/areas.png) 
18. Verify the router’s interfaces are in the correct areas
19. Verify the routers have formed adjacencies with each other
20. What change do you expect to see on R1’s routing table? Verify this (give the routing table a few seconds to converge)
21. Do you see less routes in R1’s routing table? Why or why not?
22. Configure summary routes on the Area Border Routers for the 10.0.0.0/16 and 10.1.0.0/16 networks
23. Verify R1 now sees a single summary route for 10.1.0.0/16 rather than individual routes for the 10.1.x.x networks
24. Verify R1 is receiving a summary route for the 10.1.0.0/16 network from both R2 and R5
25. R1 is routing traffic to 10.1.0.0/16 via R2 only. Why is it not load balancing the traffic through both R2 and R5?