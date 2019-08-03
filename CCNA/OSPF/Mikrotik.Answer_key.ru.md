[<img width=30 height=20 src="../../images/en.png">](Cisco.Answer_key.en.md)  [<img width=30 height=20 src="../../images/ru.png">](Cisco.Answer_key.ru.md)
## OSPF Basic Configuration

1.  Создать loopback интерфейс на каждом роутере. Назначить ему адрес 192.168.0.x/32, где x - номер роутера. Например, 192.168.0.3/32 для R3. Для RouterOS loopback это просто бридж, без портов в нем.
На каждом роутере:
```
[admin@R1] > interface bridge add name=loopback0
[admin@R1] > ip address add interface=loopback0 address=192.168.0.1/32
```
2. Создать единую область OSPF на всех роутерах. Анонсировать все сети, кроме 203.0.113.0/24. В отличие от Cisco, RouterOS не знает о loopback интерфейсе и берет в качестве Router ID самый маленький адрес из всех активных. Увидеть Router ID, автоматически назначенный роутером на самом роутере невозможно. Можно это сделать лишь на соседях. для того, чтобы назначить предопределенный Router ID, необходимо прописать его вручную.
На каждом роутере:
```
[admin@R1] > routing ospf instance set 0 router-id=192.168.0.1
[admin@R1] > routing ospf network add network=10.0.0.0/8 area=backbone
[admin@R1] > routing ospf network add network=192.168.0.0/16 area=backbone
```
Можно использовать разные подсети. Главное, чтобы адреса из этих подсетей были сконфигурированы на интерфейсах маршрутизатора

3. Какой Router ID будет у R1? 
В instance установлен Router ID 192.168.0.1. Если в instance оставить 0.0.0.0, то Router ID будет назначен автоматически, исходя из самого маленького адреса на интерфейсах.
```
[admin@R1] > routing ospf instance print
Flags: X - disabled, * - default
 0  * name="default" router-id=192.168.0.1 distribute-default=never
      redistribute-connected=no redistribute-static=no redistribute-rip=no
      redistribute-bgp=no redistribute-other-ospf=no metric-default=1
      metric-connected=20 metric-static=20 metric-rip=20 metric-bgp=auto
      metric-other-ospf=auto in-filter=ospf-in out-filter=ospf-out

```

4. Проверить соседство и смежность
```
[admin@R1] > routing ospf neighbor print
 0 instance=default router-id=192.168.0.2 address=10.0.0.2 interface=ether1
   priority=1 dr-address=10.0.0.1 backup-dr-address=10.0.0.2 state="Full"
   state-changes=44 ls-retransmits=0 ls-requests=0 db-summaries=0
   adjacency=7m25s
```

5. Проверить, что все сети 10.x.x.x и loopback есть в таблицах маршрутизации всех роутеров
```
[admin@R1] > ip route print
Flags: X - disabled, A - active, D - dynamic,
C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 #      DST-ADDRESS        PREF-SRC        GATEWAY            DISTANCE
 0 ADC  10.0.0.0/24        10.0.0.1        ether1                    0
 1 ADC  10.0.1.0/24        10.0.1.1        ether2                    0
 2 ADC  10.0.3.0/24        10.0.3.1        ether3                    0
                                           ether4
 3 ADo  10.1.0.0/24                        10.0.0.2                110
 4 ADo  10.1.1.0/24                        10.0.0.2                110
 5 ADo  10.1.2.0/24                        10.0.0.2                110
 6 ADo  10.1.3.0/24                        10.0.0.2                110
 7 ADC  192.168.0.1/32     192.168.0.1     loopback0                 0
 8 ADo  192.168.0.2/32                     10.0.0.2                110
 9 ADo  192.168.0.3/32                     10.0.0.2                110
10 ADo  192.168.0.4/32                     10.0.0.2                110
11 ADo  192.168.0.5/32                     10.0.0.2                110
```

6. Установить reference bandwidth в 1000 Mbps.
В RouterOS нет опции reference bandwidth. Стоимость может меняться только на интрефейсах

7. Какая стоимость стала у Ethernet интерфейсов? Почему?
В RouterOS нет опции reference bandwidth. Стоимость может меняться только на интрефейсах

8. Как изменение reference bandwidth влияет на маршрут к 10.1.2.0/24 на R1?
В RouterOS нет опции reference bandwidth. Стоимость может меняться только на интрефейсах

## OSPF Cost

9. Сеть 10.1.2.0/24 для R1 доступна двумя путями - через R2 и R5. Какой путь установлен в таблице маршрутизации? Почему?
Маршрут через R5 (10.0.3.2). Потому что метрика ospf-metric=40
```
[admin@R1] >  ip route print detail
Flags: X - disabled, A - active, D - dynamic,
C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 0 ADC  dst-address=10.0.0.0/24 pref-src=10.0.0.1 gateway=ether1
        gateway-status=ether1 reachable distance=0 scope=10

 1 ADC  dst-address=10.0.1.0/24 pref-src=10.0.1.1 gateway=ether2
        gateway-status=ether2 reachable distance=0 scope=10

 2 ADC  dst-address=10.0.3.0/24 pref-src=10.0.3.1 gateway=ether3,ether4
        gateway-status=ether3 reachable,ether4 reachable distance=0 scope=10

 3 ADo  dst-address=10.1.0.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=20 ospf-type=intra-area

 4 ADo  dst-address=10.1.1.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=30 ospf-type=intra-area

 5 ADo  dst-address=10.1.2.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=40 ospf-type=intra-area
6 ADo  dst-address=10.1.3.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=40 ospf-type=intra-area

 7 ADC  dst-address=192.168.0.1/32 pref-src=192.168.0.1 gateway=loopback0
        gateway-status=loopback0 reachable distance=0 scope=10

 8 ADo  dst-address=192.168.0.2/32 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=20 ospf-type=intra-area

 9 ADo  dst-address=192.168.0.3/32 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=30 ospf-type=intra-area

10 ADo  dst-address=192.168.0.4/32 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=40 ospf-type=intra-area

11 ADo  dst-address=192.168.0.5/32 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=50 ospf-type=intra-area
```
10. Реализовать прохождение трафика от R1 к 10.1.2.0/24 через R2 и R5 одновременно (балансировка).
Все интерфейсы по умолчанию имеют стоимость 10. Сейчас маршрут к 10.1.2.0/24 через R1 > R5 > R4 имеет ospf metric 30. Маршрут через R1 > R2 > R3 > R4 имеет ospf metric 40. 
Чтобы сделать одинаковую стоимость на оба пути трафика, необходимо изменить cost интерфейсов R1 > R5 и R5 > R4 на 15. Получим R1 > R5 = 15, плюс R5 > R4 = 15, плюс стоимость интерфейса к 10.1.2.0/24 = 10. Итого 40
```
[admin@R1] > routing ospf interface add interface=ether3 cost=15
```
```
[admin@R5] > routing ospf interface add interface=ether1 cost=15
[admin@R5] > routing ospf interface add interface=ether2 cost=15
```
```
[admin@R4] > routing ospf interface add interface=ether3 cost=15
```

11. Проверить, что сеть 10.1.2.0/24 доступна от R1 двумя путями
```
[admin@R1] > ip route print detail where dst-address=10.1.2.0/24
Flags: X - disabled, A - active, D - dynamic,
C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 0 ADo  dst-address=10.1.2.0/24 gateway=10.0.0.2,10.0.3.2
        gateway-status=10.0.0.2 reachable via  ether1,10.0.3.2 reachable via
               ether4
        distance=110 scope=20 target-scope=10 ospf-metric=40
        ospf-type=intra-area
```

## Анонсирование маршрута по умолчанию

12. Анонсировать сеть 203.0.113.0/24. Предотвратить анонсирование своих маршрутов в Интернет.
Добавить сеть 203.0.113.0/24 в OSPF на R4. Интерфейс Ethernet 0/3 сделать пассивным, чтобы предовратить анонсирование LSA в сеть провайдера.

```
[admin@R4] > routing ospf interface add interface=ether4 passive=yes
[admin@R4] > routing ospf network add network=203.0.113.0/24 area=backbone
```

13. Убедиться, что все роутеры получили маршрут к 203.0.113.0/24
```
[admin@R1] > ip route print
Flags: X - disabled, A - active, D - dynamic,
C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 #      DST-ADDRESS        PREF-SRC        GATEWAY            DISTANCE
 0 ADC  10.0.0.0/24        10.0.0.1        ether1                    0
 1 ADC  10.0.1.0/24        10.0.1.1        ether2                    0
 2 ADC  10.0.3.0/24        10.0.3.1        ether3                    0
                                           ether4
 3 ADo  10.1.0.0/24                        10.0.0.2                110
 4 ADo  10.1.1.0/24                        10.0.0.2                110
 5 ADo  10.1.2.0/24                        10.0.0.2                110
                                           10.0.3.2
 6 ADo  10.1.3.0/24                        10.0.3.2                110
 7 ADC  192.168.0.1/32     192.168.0.1     loopback0                 0
 8 ADo  192.168.0.2/32                     10.0.0.2                110
 9 ADo  192.168.0.3/32                     10.0.0.2                110
10 ADo  192.168.0.4/32                     10.0.0.2                110
                                           10.0.3.2
11 ADo  192.168.0.5/32                     10.0.3.2                110
12 ADo  203.0.113.0/24                     10.0.0.2                110
                                           10.0.3.2
```

14. Добавить маршрут по умолчанию на R4 с использованием шлюза 203.0.113.2
```
[admin@R4] > ip route add dst-address=0.0.0.0/0 gateway=203.0.113.2
```
15. Анонсировать маршрут по умолчанию в OSPF
```
[admin@R4] > routing ospf instance set 0 distribute-default=if-installed-as-type-1
```

16. Проверить, что все роутеры получили маршрут по умолчанию
```
[admin@R1] > ip route print
Flags: X - disabled, A - active, D - dynamic,
C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 #      DST-ADDRESS        PREF-SRC        GATEWAY            DISTANCE
 0 ADo  0.0.0.0/0                          10.0.0.2                110
                                           10.0.3.2
 1 ADC  10.0.0.0/24        10.0.0.1        ether1                    0
 2 ADC  10.0.1.0/24        10.0.1.1        ether2                    0
 3 ADC  10.0.3.0/24        10.0.3.1        ether3                    0
                                           ether4
 4 ADo  10.1.0.0/24                        10.0.0.2                110
 5 ADo  10.1.1.0/24                        10.0.0.2                110
 6 ADo  10.1.2.0/24                        10.0.0.2                110
                                           10.0.3.2
 7 ADo  10.1.3.0/24                        10.0.3.2                110
 8 ADC  192.168.0.1/32     192.168.0.1     loopback0                 0
 9 ADo  192.168.0.2/32                     10.0.0.2                110
10 ADo  192.168.0.3/32                     10.0.0.2                110
11 ADo  192.168.0.4/32                     10.0.0.2                110
                                           10.0.3.2
12 ADo  192.168.0.5/32                     10.0.3.2                110
13 ADo  203.0.113.0/24                     10.0.0.2                110
                                           10.0.3.2
```

## Области OSPF

17. Разбить домен OSPF на области (регионы). R3 и R4 оставить в backbone. R1 - internal router в Area 1. R2, R5 - ABR ![Areas](https://github.com/devi1/Labs/blob/master/CCNA/OSPF/areas.png)
R3 и R4 не требуют изменений, так как их интерфейсы уже в Are 0
Интерфейсы R1 необходимо перевести из Area 0 в Area 1. Для этого сначала создадим нужную область, а затем поместим сети в эту область
```
[admin@R1] > routing ospf area add name=area1 area-id=0.0.0.1
[admin@R1] > routing ospf network set 0 area=area1
[admin@R1] > routing ospf network set 1 area=area1

```
У R2 интерфейс ether1 остается в Area 0. ether2 перевести в Area 1. Ранее сети подключались к OSPF с широким префиксом 10.0.0.0/8. Сейчас эти сети разделяются на разные области, поэтому строку с 10.0.0.0/8 нужно удалить и более точно указать сети.

```
[admin@R2] > routing ospf area add name=area1 area-id=0.0.0.1

[admin@R2] > routing ospf network remove [find network=10.0.0.0/8]

[admin@R2] > routing ospf network add network=10.1.0.0/24 area=backbone
[admin@R2] > routing ospf network add network=10.0.0.0/24 area=area1
```
У R5 интерфейс ether2 остается в Area 0. ether1 перевести в Area 1
```
[admin@R5] > routing ospf area add name=area1 area-id=0.0.0.1

[admin@R5] > routing ospf network remove [find network=10.0.0.0/8]

[admin@R5] > routing ospf network add network=10.1.3.0/24 area=backbone
[admin@R5] > routing ospf network add network=10.0.3.0/24 area=area1
```

18. Проверить, что интерфейсы маршрутизаторов принадлежат корректным областям
```
[admin@R2] > routing ospf interface print where area=backbone
Flags: X - disabled, I - inactive, D - dynamic, P - passive
 #    INTERFACE                 COST PRI NETWORK-TYPE   AUT... AUTHENTICATIO...
 0 D  ether2                      10   1 broadcast      none
 1 DP loopback0                   10   1 broadcast      none

[admin@R2] > routing ospf interface print where area=area1
Flags: X - disabled, I - inactive, D - dynamic, P - passive
 #    INTERFACE                 COST PRI NETWORK-TYPE   AUT... AUTHENTICATIO...
 0 D  ether1                      10   1 broadcast      none
```

19. Проверить состояние соседства/смежности
```
[admin@R1] > routing ospf neighbor pr
 0 instance=default router-id=192.168.0.5 address=10.0.3.2 interface=ether3
   priority=1 dr-address=10.0.3.1 backup-dr-address=10.0.3.2 state="Full"
   state-changes=45 ls-retransmits=0 ls-requests=0 db-summaries=0
   adjacency=26s

 1 instance=default router-id=192.168.0.2 address=10.0.0.2 interface=ether1
   priority=1 dr-address=10.0.0.1 backup-dr-address=10.0.0.2 state="Full"
   state-changes=5 ls-retransmits=0 ls-requests=0 db-summaries=0
   adjacency=12m17s
```

20. Какие изменения произошли в таблице маршрутизации R1?
Сети за R2 и R5 обозначены как Inter Area маршруты. Кроме маршрута по умолчанию, который обозначен как external
```
[admin@R1] > ip route print detail
Flags: X - disabled, A - active, D - dynamic,
C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 0 ADo  dst-address=0.0.0.0/0 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=41 ospf-type=external-type-1

 1 ADC  dst-address=10.0.0.0/24 pref-src=10.0.0.1 gateway=ether1
        gateway-status=ether1 reachable distance=0 scope=10

 2 ADC  dst-address=10.0.1.0/24 pref-src=10.0.1.1 gateway=ether2
        gateway-status=ether2 reachable distance=0 scope=10

 3 ADC  dst-address=10.0.3.0/24 pref-src=10.0.3.1 gateway=ether3,ether4
        gateway-status=ether3 reachable,ether4 reachable distance=0 scope=10

 4 ADo  dst-address=10.1.0.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=20 ospf-type=inter-area

 5 ADo  dst-address=10.1.1.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=30 ospf-type=inter-area

 6 ADo  dst-address=10.1.2.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=40 ospf-type=inter-area

 7 ADo  dst-address=10.1.3.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=45 ospf-type=inter-area

 8 ADC  dst-address=192.168.0.1/32 pref-src=192.168.0.1 gateway=loopback0
        gateway-status=loopback0 reachable distance=0 scope=10

 9 ADo  dst-address=192.168.0.2/32 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=20 ospf-type=inter-area

10 ADo  dst-address=192.168.0.3/32 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=30 ospf-type=inter-area

11 ADo  dst-address=192.168.0.4/32 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=40 ospf-type=inter-area

12 ADo  dst-address=192.168.0.5/32 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=55 ospf-type=inter-area

13 ADo  dst-address=203.0.113.0/24 gateway=10.0.0.2
        gateway-status=10.0.0.2 reachable via  ether1 distance=110 scope=20
        target-scope=10 ospf-metric=40 ospf-type=inter-area

```

21. Изменилось ли количество маршрутов в таблице маршрутизации R1? Почему?
Количество маршрутов в таблице маршрутизации R1 не изменилось. Это связано с тем, что не применялась аггрегация (суммирование) маршрутов OSPF. Чтобы уменьшить количество маршуртов в таблицах маршуртизации, необходимо вручную сконфигурировать суммирование.

22. Настроить суммирование маршрутов на ABR для сетей 10.0.0.0/16 и 10.1.0.0/16
```
[admin@R2] > routing ospf area range add area=backbone range=10.1.0.0/16
[admin@R2] > routing ospf area range add area=area1  range=10.0.0.0/16
```
```
R5(config-if)#router ospf 1
R5(config-router)#area 0 range 10.1.0.0 255.255.0.0
R5(config-router)#area 1 range 10.0.0.0 255.255.0.0
```
23. Проверить, что R1 получил агрегированный маршрут до 10.1.0.0/16 вместо точных маршрутов до каждой подсети
```
[admin@R1] > ip route print
Flags: X - disabled, A - active, D - dynamic,
C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 #      DST-ADDRESS        PREF-SRC        GATEWAY            DISTANCE
 0 ADo  0.0.0.0/0                          10.0.0.2                110
 1 ADo  10.0.0.0/16                        10.0.0.2                110
 2 ADC  10.0.0.0/24        10.0.0.1        ether1                    0
 3 ADC  10.0.1.0/24        10.0.1.1        ether2                    0
 4 ADC  10.0.3.0/24        10.0.3.1        ether3                    0
                                           ether4
 5 ADo  10.1.0.0/16                        10.0.0.2                110
 6 ADC  192.168.0.1/32     192.168.0.1     loopback0                 0
 7 ADo  192.168.0.2/32                     10.0.0.2                110
 8 ADo  192.168.0.3/32                     10.0.0.2                110
 9 ADo  192.168.0.4/32                     10.0.0.2                110
10 ADo  192.168.0.5/32                     10.0.0.2                110
11 ADo  203.0.113.0/24                     10.0.0.2                110
```
24. Проверить, что R1 получил два агрегированных маршрута до 10.1.0.0/16 - от R2 и R5
```
[admin@R1] > routing ospf lsa print detail
...
 instance=default area=area1 type=summary-network id=10.1.0.0
   originator=192.168.0.2 sequence-number=0x80000002 age=104 checksum=0x22A0
   options="E" body=
     netmask=255.255.0.0
     metric=35

 instance=default area=area1 type=summary-network id=10.1.0.0
   originator=192.168.0.5 sequence-number=0x80000002 age=71 checksum=0x10AF
   options="E" body=
     netmask=255.255.0.0
     metric=35
...
```
25. R1 отправляет трафик до 10.1.0.0/16 только через R2. Почему нет балансировки?
Стоимость интерфейса R1 > R5 выше, чем у R1 > R2

```
[admin@R1] > routing ospf interface print
Flags: X - disabled, I - inactive, D - dynamic, P - passive
 #    INTERFACE                 COST PRI NETWORK-TYPE   AUT... AUTHENTICATIO...
 0    ether3                      15   1 default        none
 1 D  ether2                      10   1 broadcast      none
 2 D  ether4                      10   1 broadcast      none
 3 DP loopback0                   10   1 broadcast      none
 4 D  ether1                      10   1 broadcast      none
```