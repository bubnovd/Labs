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

### Static EtherChannel Configuration
6. Convert the existing uplinks between SW1 and SW2 to static EtherChannel
7. Verify the EtherChannels come up
8. How much total bandwidth is available between the PCs attached to SW3 and the PCs attached to SW4 now?

## VLAN and Inter-VLAN Routing
### VTP, Access and Trunk Ports
9. All routers and switches are in a factory default state. View the VLAN database on SW1 to verify no VLANs have been added 
10. View the default switchport status on the link from SW1 to SW2
11. Configure SW1 as a VTP Server in the VTP domain bubnovd.net
12. SW2 must not synchronise its VLAN database with SW1
13. SW3 and SW4 must learn VLAN information from SW1. VLANs should not be edited on SW3 and SW4
14. Add the Eng (VID 10), Sales (VID11) and Native (VID 199) VLANs on all switches
15. Verify the VLANs are in the database on each switch
16. Configure the trunk links to use VLAN 199 as the native VLAN for better security
17. Configure the switchports connected to the PCs with the correct VLAN configuration
18. Verify the VPC1 has connectivity to VPC2
19. Verify the VPC11 has connectivity to VPC12

>DTP. Is it need here?