============================================================
  ASKAY UNIVERSITY OF TECHNOLOGY
  School of Computing and Information Technology
  Network Design & VLAN Configuration Project
============================================================
  FILE    : 00_README-CONFIG-INDEX.txt
  PURPOSE : Master Index of All Configuration Files
============================================================

PROJECT OVERVIEW
----------------
Network: 192.168.50.0/24
Design : Hierarchical — Access → Core → Distribution (Router)
Tool   : Cisco Packet Tracer
Method : VLSM Subnetting + VLAN Segmentation + Router-on-a-Stick + DHCP


NETWORK TOPOLOGY SUMMARY
-------------------------

  [CORE-ROUTER]
      |  G0/0 (trunk — all VLANs)
      |
  [CORE-SWITCH]
   |      |      |      |
  G0/2  G0/3  G0/4  G0/5
   |      |      |      |
  SW-    SW-    SW-    SW-
  LAB   STAFF  PGRAD  SERVER
   |      |      |      |
  PCs    PCs  PCs+AP  Servers


CONFIGURATION FILE ORDER
-------------------------
Apply configurations in the following sequence:

  ORDER  FILE                                       DEVICE
  -----  -----------------------------------------  --------------------------
  01     01_ACCESS-SW-COMPUTER-LAB_VLAN10.txt       SW-COMPUTER-LAB
  02     02_ACCESS-SW-STAFF-OFFICES_VLAN20.txt      SW-STAFF-OFFICES
  03     03_ACCESS-SW-POSTGRAD-ROOM_VLAN30.txt      SW-POSTGRAD-ROOM
  04     04_ACCESS-SW-SERVER-ROOM_VLAN40.txt        SW-SERVER-ROOM
  05     05_CORE-SWITCH_ALL-VLANS.txt               CORE-SWITCH
  06     06_CORE-ROUTER_INTERVLAN-DHCP.txt          CORE-ROUTER
  07     07_WIRELESS-AP_POSTGRAD-ROOM.txt           AP-POSTGRAD-ROOM
  08     08_VERIFICATION-AND-TROUBLESHOOTING.txt    All Devices (reference)


IP ADDRESS QUICK REFERENCE
---------------------------

  VLAN  DEPT             NETWORK              GATEWAY         BROADCAST
  ----  ---------------  -------------------  --------------  ----------------
  10    Computer Lab     192.168.50.0/26      192.168.50.1    192.168.50.63
  20    Staff Offices    192.168.50.64/27     192.168.50.65   192.168.50.95
  30    Postgrad Room    192.168.50.96/28     192.168.50.97   192.168.50.111
  40    Server Room      192.168.50.112/29    192.168.50.113  192.168.50.119


DHCP ASSIGNABLE RANGES (after exclusions)
------------------------------------------

  VLAN  ASSIGNABLE RANGE
  ----  -------------------------------------
  10    192.168.50.16  – 192.168.50.62
  20    192.168.50.71  – 192.168.50.94
  30    192.168.50.102 – 192.168.50.110
  40    Static IPs only (no DHCP assignment)

============================================================
  END OF INDEX
============================================================
