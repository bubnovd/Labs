[<img width=30 height=20 src="../../images/en.png">](README.en.md)  [<img width=30 height=20 src="../../images/ru.png">](README.md)
# Switching
В файле `CCNA_Switching.zip` находится схема сети для импорта в эмулятор EVE-NG.
Используемые устройства:
- CCNA_Switching - Cisco:
  - Router - `L3-ADVENTERPRISEK9-M-15.4-2T`
  - Switch - `L2-ADVENTERPRISEK9-M-15.2-20150703`


*Из-за ограничений образов Cisco IOU, применяемых в лабе, все интерфейсы - десятимегабитный Ethernet*

![Lab1](https://github.com/devi1/Labs/blob/master/CCNA/Switching/lab1.png) 
## Аггрегация линков
### LACP EtherChannel
1. У коммутаторов уровня доступа по два аплинка. Какая пропускная способность сети между хостами VPC1, VPC11 и VPC2, VPC12?
2. Переделайте существующие аплинки от SW3 к SW1 и SW2 в LACP EtherChannel. Не забудьте подписать интерфейсы, чтобы не запутаться в будущем
3. Проверьте, что EtherChannel поднялся. *В моем случае LACP не заработал. Видимо, особенности GCP. Другие типы EtherChannel завести удалось*

### PAgP EtherChannel
4. Переделайте существующие аплинки от SW4 к SW1 и SW2 в PAgP EtherChannel
5. Проверьте, что EtherChannel поднялся

### Static EtherChannel
6. Переделайте существующие линки между SW1 и SW2 в Static EtherChannel
7. Проверьте, что EtherChannel поднялся
8. Какая пропускная способность сети между хостами VPC1, VPC11 и VPC2, VPC12 сейчас?

## VTP, Access, Trunk
9. Маршрутизаторы и коммутаторы в дефолтном состоянии. Убедитесь, что на SW1 нет сконфигурированных VLAN'ов
10. Проверьте состояние switchport на линке между SW1 и SW2
11. Настройте SW1 как VTP Server в домене bubnovd.net
12. Настройте SW2 так, чтобы он не синхронизировал список VLAN с SW1
13. Настройте синхронизацию базы данных VLAN на SW3 и SW4. Они должны брать информацию о VLAN с SW1
14. Добавьте VLAN'ы Eng (VID 10), Sales (VID 11), Native (VID 199) на все коммутаторы
15. Проверьте, что все VLAN'ы теперь существуют на всех коммутаторах
16. Настройте транковые порты на использование 199 VLAN'a как Native. Это повысит безопасность сети
17. Настройте access порты на SW3 и SW4 в соответствии со схемой
18. Проверьте связь между VPC1 и VPC2
19. Проверьте связь между VPC11 и VPC12

>DTP. Надо ли здесь?