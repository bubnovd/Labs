## OSPF Basic Configuration

1.  Создать loopback интерфейс на каждом роутере. Назначить ему адрес 192.168.0.x/32, где x - номер роутера. Например, 192.168.0.3/32 для R3.
На каждом роутере:
```
R1(config)#interface loopback0
R1(config-if)#ip address 192.168.0.1 255.255.255.255
```
2. Создать единую область OSPF на всех роутерах. Анонсировать все сети, кроме 203.0.113.0/24
На каждом роутере:
```
R1(config)#router ospf 1
R1(config-router)#network 10.0.0.0 0.255.255.255 area 0
R1(config-router)#network 192.168.0.0 0.0.0.255 area 0
```
Можно использовать разные подсети. Главное, чтобы адреса из этих подсетей были сконфигурированы на интерфейсах маршрутизатора

3. Какой Router ID будет у R1? 
Адрес на интерфейсе loopback R1 - 192.168.0.1. Этот адрес и будет использоваться в качестве Router ID
```
R1#sh ip protocols
*** IP Routing is NSF aware ***

Routing Protocol is "ospf 1"
Outgoing update filter list for all interfaces is not set
Incoming update filter list for all interfaces is not set
Router ID 192.168.0.1
Number of areas in this router is 1. 1 normal 0 stub 0 nssa
Maximum path: 4
Routing for Networks:
10.0.0.0 0.255.255.255 area 0
192.168.0.0 0.0.0.255 area 0
Routing Information Sources:
Gateway Distance Last Update
10.1.1.2 110 00:00:25
10.1.0.2 110 00:00:25
10.1.3.2 110 00:00:25
203.0.113.1 110 00:00:25
Distance: (default is 110)
```

4. Проверить соседство и смежность
```
R1#show ip ospf neighbor
Neighbor ID Pri State Dead Time Address Interface
192.168.0.5 1 FULL/BDR 00:00:31 10.0.3.2 FastEthernet3/0
192.168.0.2 1 FULL/DR 00:00:39 10.0.0.2 FastEthernet0/0
```

5. Проверить, что все сети 10.x.x.x и loopback есть в таблицах маршрутизации всех роутеров
```
R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is not set
10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0
O 10.1.0.0/24 [110/2] via 10.0.0.2, 00:03:16, FastEthernet0/0
O 10.1.1.0/24 [110/3] via 10.0.3.2, 00:03:16, FastEthernet3/0
[110/3] via 10.0.0.2, 00:03:16, FastEthernet0/0
O 10.1.2.0/24 [110/3] via 10.0.3.2, 00:03:16, FastEthernet3/0
O 10.1.3.0/24 [110/2] via 10.0.3.2, 00:03:16, FastEthernet3/0
192.168.0.0/32 is subnetted, 5 subnets
C 192.168.0.1 is directly connected, Loopback0
O 192.168.0.2 [110/2] via 10.0.0.2, 00:03:16, FastEthernet0/0
O 192.168.0.3 [110/3] via 10.0.0.2, 00:03:16, FastEthernet0/0
O 192.168.0.4 [110/3] via 10.0.3.2, 00:03:16, FastEthernet3/0
O 192.168.0.5 [110/2] via 10.0.3.2, 00:03:16, FastEthernet3/0
```

6. Установить reference bandwidth в 10 Mbps.
На всех роутерах
```
R1(config)#router ospf 1
R1(config-router)#auto-cost reference-bandwidth 10000
```

7. Какая стоимость у FastEthernet интерфейсов? Почему?
OSPF Cost = Reference bandwidth / Interface bandwidth. 100000 / 100 = 1000
```
R1#show ip ospf interface FastEthernet 0/0
FastEthernet0/0 is up, line protocol is up
Internet Address 10.0.0.1/24, Area 0, Attached via Network
Statement
Process ID 1, Router ID 192.168.0.1, Network Type BROADCAST, Cost:
1000
Topology-MTID Cost Disabled Shutdown Topology Name
0 1000 no no Base
Transmit Delay is 1 sec, State BDR, Priority 1
Designated Router (ID) 10.1.0.2, Interface address 10.0.0.2
Backup Designated router (ID) 192.168.0.1, Interface address
10.0.0.1
Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit
5
oob-resync timeout 40
Hello due in 00:00:02
Supports Link-local Signaling (LLS)
Cisco NSF helper support enabled
IETF NSF helper support enabled
Index 1/1, flood queue length 0
Next 0x0(0)/0x0(0)
Last flood scan length is 1, maximum is 1
Last flood scan time is 0 msec, maximum is 4 msec
Neighbor Count is 1, Adjacent neighbor count is 1
Adjacent with neighbor 10.1.0.2 (Designated Router)
Suppress hello for 0 neighbor(s)
```

8. Как изменение reference bandwidth влияет на маршрут к 10.1.2.0/24 на R1?
The cost changes from 3 to 3000.
Before reference bandwidth change:

```
R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is not set
10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0
O 10.1.0.0/24 [110/2] via 10.0.0.2, 00:03:16, FastEthernet0/0
O 10.1.1.0/24 [110/3] via 10.0.3.2, 00:03:16, FastEthernet3/0
[110/3] via 10.0.0.2, 00:03:16, FastEthernet0/0
O 10.1.2.0/24 [110/3] via 10.0.3.2, 00:03:16, FastEthernet3/0
O 10.1.3.0/24 [110/2] via 10.0.3.2, 00:03:16, FastEthernet3/0
192.168.0.0/32 is subnetted, 5 subnets
C 192.168.0.1 is directly connected, Loopback0
O 192.168.0.2 [110/2] via 10.0.0.2, 00:03:16, FastEthernet0/0
O 192.168.0.3 [110/3] via 10.0.0.2, 00:03:16, FastEthernet0/0
O 192.168.0.4 [110/3] via 10.0.3.2, 00:03:16, FastEthernet3/0
O 192.168.0.5 [110/2] via 10.0.3.2, 00:03:16, FastEthernet3/0
```
After reference bandwidth change:
```
R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is not set
10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0
O 10.1.0.0/24 [110/2000] via 10.0.0.2, 00:00:22, FastEthernet0/0
O 10.1.1.0/24 [110/3000] via 10.0.3.2, 00:00:38, FastEthernet3/0
[110/3000] via 10.0.0.2, 00:00:22, FastEthernet0/0
O 10.1.2.0/24 [110/3000] via 10.0.3.2, 00:00:39, FastEthernet3/0
O 10.1.3.0/24 [110/2000] via 10.0.3.2, 00:00:39, FastEthernet3/0
192.168.0.0/32 is subnetted, 5 subnets
C 192.168.0.1 is directly connected, Loopback0
O 192.168.0.2 [110/1001] via 10.0.0.2, 00:00:23, FastEthernet0/0
O 192.168.0.3 [110/2001] via 10.0.0.2, 00:00:23, FastEthernet0/0
O 192.168.0.4 [110/2001] via 10.0.3.2, 00:00:39, FastEthernet3/0
O 192.168.0.5 [110/1001] via 10.0.3.2, 00:00:39, FastEthernet3/0
```
## OSPF Cost

9. There are two possible paths which R1 could use to reach the 10.1.2.0/24 network – either through R2 or R5. Which route is in the routing table?
The path via R5 at 10.0.3.2.
```
R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is not set
10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0
O 10.1.0.0/24 [110/2000] via 10.0.0.2, 00:00:22, FastEthernet0/0
O 10.1.1.0/24 [110/3000] via 10.0.3.2, 00:00:38, FastEthernet3/0
[110/3000] via 10.0.0.2, 00:00:22, FastEthernet0/0
O 10.1.2.0/24 [110/3000] via 10.0.3.2, 00:00:39, FastEthernet3/0
O 10.1.3.0/24 [110/2000] via 10.0.3.2, 00:00:39, FastEthernet3/0
192.168.0.0/32 is subnetted, 5 subnets
C 192.168.0.1 is directly connected, Loopback0
O 192.168.0.2 [110/1001] via 10.0.0.2, 00:00:23, FastEthernet0/0
O 192.168.0.3 [110/2001] via 10.0.0.2, 00:00:23, FastEthernet0/0
O 192.168.0.4 [110/2001] via 10.0.3.2, 00:00:39, FastEthernet3/0
O 192.168.0.5 [110/1001] via 10.0.3.2, 00:00:39, FastEthernet3/0
```
10. Change this so that traffic from R1 to 10.1.2.0/24 will be load balanced via both R2 and R5. Since we changed the reference bandwidth, all interfaces have a cost of 1000. The current path from R1 > R5 > R4 has a cost of 3000 (the cost of the destination interface itself is also counted in the total cost).

The path from R1 > R2 > R3 > R4 has a cost of 4000.

The easiest way to configure both paths to have the same cost is to configure the links from R1 > R5 and R5 > R4 to have a cost of 1500 each. (R1 > R5 = 1500, plus R5 > R4 = 1500, plus cost of 10.1.2.0/24 interface on R4 = 1000. Total = 4000).
```
R1(config)#int f3/0
R1(config-if)#ip ospf cost 1500

R5(config)#int f2/0
R5(config-if)# ip ospf cost 1500
R5(config)#int f3/0
R5(config-if)# ip ospf cost 1500

R4(config)#int f2/0
R4(config-if)# ip ospf cost 1500
```
11. Verify that traffic to the 10.1.2.0/24 network from R1 is load balanced via both R2 and R5.
```
R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is not set
10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0
O 10.1.0.0/24 [110/2000] via 10.0.0.2, 00:33:40, FastEthernet0/0
O 10.1.1.0/24 [110/3000] via 10.0.0.2, 00:33:40, FastEthernet0/0
O 10.1.2.0/24 [110/4000] via 10.0.3.2, 00:00:32, FastEthernet3/0
[110/4000] via 10.0.0.2, 00:00:32, FastEthernet0/0
O 10.1.3.0/24 [110/3000] via 10.0.3.2, 00:00:32, FastEthernet3/0
192.168.0.0/32 is subnetted, 5 subnets
C 192.168.0.1 is directly connected, Loopback0
O 192.168.0.2 [110/1001] via 10.0.0.2, 00:33:40, FastEthernet0/0
O 192.168.0.3 [110/2001] via 10.0.0.2, 00:33:40, FastEthernet0/0
O 192.168.0.4 [110/3001] via 10.0.3.2, 00:00:32, FastEthernet3/0
[110/3001] via 10.0.0.2, 00:00:32, FastEthernet0/0
O 192.168.0.5 [110/1501] via 10.0.3.2, 00:26:30, FastEthernet3/0
```

## Default Route Injection

12. Ensure that all routers have a route to the 203.0.113.0/24 network. Internal routes must not be advertised to the Service Provider at 203.0.113.2. The 203.0.113.0/24 network must be added to the OSPF process on R4, and interface FastEthernet 3/0 facing the service provider configured as a passive interface to avoid sending out internal network information.

```
R4(config)#router ospf 1
R4(config-router)#passive-interface f3/0
R4(config-router)#network 203.0.113.0 0.0.0.255 area 0`

13. Verify that all routers have a path to the 203.0.113.0/24 network.
`R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is not set
10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0
O 10.1.0.0/24 [110/2000] via 10.0.0.2, 00:56:05, FastEthernet0/0

O 10.1.1.0/24 [110/3000] via 10.0.0.2, 00:56:05, FastEthernet0/0
O 10.1.2.0/24 [110/4000] via 10.0.3.2, 00:22:57, FastEthernet3/0
[110/4000] via 10.0.0.2, 00:22:57, FastEthernet0/0
O 10.1.3.0/24 [110/3000] via 10.0.3.2, 00:22:57, FastEthernet3/0
192.168.0.0/32 is subnetted, 5 subnets
C 192.168.0.1 is directly connected, Loopback0
O 192.168.0.2 [110/1001] via 10.0.0.2, 00:56:05, FastEthernet0/0
O 192.168.0.3 [110/2001] via 10.0.0.2, 00:56:05, FastEthernet0/0
O 192.168.0.4 [110/3001] via 10.0.3.2, 00:22:57, FastEthernet3/0
[110/3001] via 10.0.0.2, 00:22:57, FastEthernet0/0
O 192.168.0.5 [110/1501] via 10.0.3.2, 00:48:55, FastEthernet3/0
O 203.0.113.0/24 [110/4000] via 10.0.3.2, 00:01:46, FastEthernet3/0
[110/4000] via 10.0.0.2, 00:01:46, FastEthernet0/0
```
14. Configure a default static route on R4 to the Internet via the service provider at 203.0.113.2
```R4(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2```

15. Ensure that all other routers learn via OSPF how to reach the Internet.
```
R4(config)#router ospf 1
R4(config-router)#default-information originate
```

16. Verify all routers have a route to the Internet.
```
R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is 10.0.3.2 to network 0.0.0.0
O*E2 0.0.0.0/0 [110/1] via 10.0.3.2, 00:00:41, FastEthernet3/0
[110/1] via 10.0.0.2, 00:00:41, FastEthernet0/0
10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0
O 10.1.0.0/24 [110/2000] via 10.0.0.2, 00:59:13, FastEthernet0/0
O 10.1.1.0/24 [110/3000] via 10.0.0.2, 00:59:13, FastEthernet0/0
O 10.1.2.0/24 [110/4000] via 10.0.3.2, 00:26:05, FastEthernet3/0
[110/4000] via 10.0.0.2, 00:26:05, FastEthernet0/0
O 10.1.3.0/24 [110/3000] via 10.0.3.2, 00:26:05, FastEthernet3/0
192.168.0.0/32 is subnetted, 5 subnets
C 192.168.0.1 is directly connected, Loopback0
O 192.168.0.2 [110/1001] via 10.0.0.2, 00:59:13, FastEthernet0/0
O 192.168.0.3 [110/2001] via 10.0.0.2, 00:59:13, FastEthernet0/0
O 192.168.0.4 [110/3001] via 10.0.3.2, 00:26:05, FastEthernet3/0
[110/3001] via 10.0.0.2, 00:26:05, FastEthernet0/0
O 192.168.0.5 [110/1501] via 10.0.3.2, 00:52:03, FastEthernet3/0
O 203.0.113.0/24 [110/4000] via 10.0.3.2, 00:04:54, FastEthernet3/0
[110/4000] via 10.0.0.2, 00:04:54, FastEthernet0/0
```
## Multi-Area OSPF

17. Convert the network to use multi-area OSPF. R3 and R4 should be backbone routers, R1 a normal router in Area 1, and R2 and R5 ABRs as shown in the diagram below.

R3 and R4 require no change as all their interfaces are already in Area 0.

R1’s interfaces need to be reconfigured to be in Area 1 rather than Area 0.
```
R1#show run | section ospf
ip ospf cost 1500
router ospf 1
auto-cost reference-bandwidth 100000
network 10.0.0.0 0.255.255.255 area 0
network 192.168.0.0 0.0.0.255 area 0

R1(config)#router ospf 1
R1(config-router)#network 10.0.0.0 0.255.255.255 area 1
R1(config-router)#network 192.168.0.0 0.0.0.255 area 1
```
R2 interface FastEthernet 1/0 should remain in Area 0. FastEthernet 0/0 needs to be reconfigured to be in Area 1. I used a 10.0.0.0/8 network statement originally so I need to remove that and add more granular statements.

```
R2#sh run | section ospf
router ospf 1
auto-cost reference-bandwidth 100000
network 10.0.0.0 0.255.255.255 area 0
network 192.168.0.0 0.0.0.255 area 0

R2(config)#router ospf 1
R2(config-router)#no network 10.0.0.0 0.255.255.255 area 0
R2(config-router)#network 10.1.0.0 0.0.0.255 area 0
R2(config-router)#network 10.0.0.0 0.0.0.255 area 1
```
R5 interface FastEthernet 2/0 should remain in Area 0. FastEthernet 3/0 needs to be reconfigured to be in Area 1.
```
R5#sh run | section ospf
ip ospf cost 1500
ip ospf cost 1500
router ospf 1
auto-cost reference-bandwidth 100000
network 10.0.0.0 0.255.255.255 area 0
network 192.168.0.0 0.0.0.255 area 0

R5(config)#router ospf 1
R5(config-router)#no network 10.0.0.0 0.255.255.255 area 0
R5(config-router)#network 10.1.3.0 0.0.0.255 area 0
R5(config-router)#network 10.0.3.0 0.0.0.255 area 1
```
18. Verify the router’s interfaces are in the correct areas.
```
R2#sh ip ospf interface brief
Interface PID Area IP Address/Mask Cost State Nbrs F/C
Lo0 1 0 192.168.0.2/32 1 LOOP 0/0
Fa1/0 1 0 10.1.0.2/24 1000 BDR 1/1
Fa0/0 1 1 10.0.0.2/24 1000 BDR 1/1
```
19. Verify the routers have formed adjacencies with each other.
```
R1#sh ip ospf neighbor
Neighbor ID Pri State Dead Time Address Interface
192.168.0.5 1 FULL/BDR 00:00:33 10.0.3.2 FastEthernet3/0
192.168.0.2 1 FULL/BDR 00:00:31 10.0.0.2 FastEthernet0/0
```
20. What change do you expect to see on R1’s routing table? Verify this (give the routing table a few seconds to converge). The networks beyond R2 and R5 will appear as Inter Area routes (apart from the default route which will appear as an external route as it was redistributed into OSPF).
```
R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is 10.0.3.2 to network 0.0.0.0
O*E2 0.0.0.0/0 [110/1] via 10.0.3.2, 00:05:54, FastEthernet3/0
[110/1] via 10.0.0.2, 00:10:20, FastEthernet0/0
10.0.0.0/8 is variably subnetted, 12 subnets, 2 masks
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0

O IA 10.1.0.0/24 [110/2000] via 10.0.0.2, 00:10:20, FastEthernet0/0
O IA 10.1.1.0/24 [110/3000] via 10.0.0.2, 00:10:20, FastEthernet0/0
O IA 10.1.2.0/24 [110/4000] via 10.0.3.2, 00:05:54, FastEthernet3/0
[110/4000] via 10.0.0.2, 00:10:20, FastEthernet0/0
O IA 10.1.3.0/24 [110/3000] via 10.0.3.2, 00:05:54, FastEthernet3/0
192.168.0.0/32 is subnetted, 5 subnets
C 192.168.0.1 is directly connected, Loopback0
O IA 192.168.0.2 [110/1001] via 10.0.0.2, 00:10:20, FastEthernet0/0
O IA 192.168.0.3 [110/2001] via 10.0.0.2, 00:10:20, FastEthernet0/0
O IA 192.168.0.4 [110/3001] via 10.0.3.2, 00:05:54, FastEthernet3/0
[110/3001] via 10.0.0.2, 00:10:20, FastEthernet0/0
O IA 192.168.0.5 [110/1501] via 10.0.3.2, 00:05:54, FastEthernet3/0
O IA 203.0.113.0/24 [110/4000] via 10.0.3.2, 00:05:54, FastEthernet3/0
[110/4000] via 10.0.0.2, 00:10:20, FastEthernet0/0
```

21. Do you see less routes in R1’s routing table? Why or why not?
R1 has the same amount of routes in its routing table because OSPF does not perform automatic summarisation. You must configure manual summarisation to reduce the size of the routing table.

22. Configure summary routes on the Area Border Routers for the 10.0.0.0/16 and 10.1.0.0/16 networks.
```
R2(config)#router ospf 1
R2(config-router)#area 0 range 10.1.0.0 255.255.0.0
R2(config-router)#area 1 range 10.0.0.0 255.255.0.0
```
```
R5(config-if)#router ospf 1
R5(config-router)#area 0 range 10.1.0.0 255.255.0.0
R5(config-router)#area 1 range 10.0.0.0 255.255.0.0
```
23. Verify R1 now sees a single summary route for 10.1.0.0/16 rather than individual routes for the 10.1.x.x networks.
```
R1#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2
i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
ia - IS-IS inter area, * - candidate default, U - per-user static route
o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
+ - replicated route, % - next hop override

Gateway of last resort is 10.0.3.2 to network 0.0.0.0
O*E2 0.0.0.0/0 [110/1] via 10.0.3.2, 00:27:35, FastEthernet3/0
[110/1] via 10.0.0.2, 00:26:43, FastEthernet0/0
10.0.0.0/8 is variably subnetted, 10 subnets, 3 masks
O IA 10.0.0.0/16 [110/6000] via 10.0.3.2, 00:03:47, FastEthernet3/0
C 10.0.0.0/24 is directly connected, FastEthernet0/0
L 10.0.0.1/32 is directly connected, FastEthernet0/0
C 10.0.1.0/24 is directly connected, FastEthernet1/0
L 10.0.1.1/32 is directly connected, FastEthernet1/0
C 10.0.2.0/24 is directly connected, FastEthernet2/0
L 10.0.2.1/32 is directly connected, FastEthernet2/0
C 10.0.3.0/24 is directly connected, FastEthernet3/0
L 10.0.3.1/32 is directly connected, FastEthernet3/0
O IA 10.1.0.0/16 [110/2000] via 10.0.0.2, 00:03:56, FastEthernet0/0
192.168.0.0/32 is subnetted, 3 subnets
C 192.168.0.1 is directly connected, Loopback0
O IA 192.168.0.3 [110/2001] via 10.0.0.2, 00:28:43, FastEthernet0/0
O IA 192.168.0.4 [110/3001] via 10.0.3.2, 00:26:43, FastEthernet3/0
[110/3001] via 10.0.0.2, 00:26:43, FastEthernet0/0
O IA 203.0.113.0/24 [110/4000] via 10.0.3.2, 00:26:43, FastEthernet3/0
[110/4000] via 10.0.0.2, 00:26:43, FastEthernet0/0
```
24. Verify R1 is receiving a summary route for the 10.1.0.0/16 network from both R2 and R5.
```
R1#sh ip ospf database

OSPF Router with ID (192.168.0.1) (Process ID 1)

Router Link States (Area 1)

Link ID ADV Router Age Seq# Checksum Link count
192.168.0.1 192.168.0.1 18 0x80000005 0x00536E 5
192.168.0.2 192.168.0.2 27 0x80000003 0x0069ED 1
192.168.0.5 192.168.0.5 1890 0x80000003 0x00C490 1

Net Link States (Area 1)

Link ID ADV Router Age Seq# Checksum
10.0.0.1 192.168.0.1 18 0x80000002 0x00DF0E
10.0.3.1 192.168.0.1 18 0x80000002 0x00E8FE

Summary Net Link States (Area 1)

Link ID ADV Router Age Seq# Checksum
10.0.0.0 192.168.0.5 509 0x80000001 0x0044DA
10.1.0.0 192.168.0.2 523 0x80000002 0x0015C4
10.1.0.0 192.168.0.5 475 0x80000003 0x009A45
192.168.0.3 192.168.0.2 27 0x80000002 0x00DD99
192.168.0.3 192.168.0.5 1889 0x80000002 0x0098F9
192.168.0.4 192.168.0.2 27 0x80000002 0x000783
192.168.0.4 192.168.0.5 1889 0x80000002 0x005B22
203.0.113.0 192.168.0.2 27 0x80000002 0x00D0FE
203.0.113.0 192.168.0.5 1889 0x80000002 0x00259D

Summary ASB Link States (Area 1)

Link ID ADV Router Age Seq# Checksum
192.168.0.4 192.168.0.2 27 0x80000002 0x00EE9B

192.168.0.4 192.168.0.5 1889 0x80000002 0x00433A

Type-5 AS External Link States

Link ID ADV Router Age Seq# Checksum Tag
0.0.0.0 192.168.0.4 207 0x80000002 0x00152F 1
```
25. R1 is routing traffic to 10.1.0.0/16 via R2 only. Why is it not load balancing the traffic through both R2 and R5?
We configured the link from R1 to R5 to have a higher cost than the link from R1 to R2 earlier.

```
R1#sh run int f3/0
Building configuration...

Current configuration : 100 bytes
!
interface FastEthernet3/0
ip address 10.0.3.1 255.255.255.0
ip ospf cost 1500
duplex full
end
```