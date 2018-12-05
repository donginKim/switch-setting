Cisco Switch setting - Nexus 3000
===================================
윈도우 환경에서 세팅을 하였습니다.

1. Hardware setting
----------------
필요한 목록 : putty, Uport (1110 시리즈 사용), 시리얼 케이블

```
1 PC와 Uport를 연결하여 장치관리자에서 드라이버를 설정한다.
2 드라이버 설정 오류 시 https://www.moxa.com/product/UPort_1110.htm 에서 드라이버를 다운 받아 설치 하여 잡으시면 됩니다.
3 연결이 정상적으로 완료 되면 putty Serial Connection을 통해 Switch의 Console 창으로 들어가지면 하드웨어 세팅이 완료 됩니다.
```


2. Switch Setting
----------------
1) 처음 스위치 시동 후 

```
Abort Power On Auto Provisioning [yes - continue with normal setup, skip - bypass password and basic configuration, no - continue with Power On Auto Provisioning] (yes/skip/no)[no]: yes

Disabling POAP……

**Do you want to enforce secure password standard (yes/no): yes**

Enter the password for "admin":

  **Enter the password for "admin": (특문, 대문자 포함 8자 이상 – Nsr1234!)**
  **Confirm the password for "admin": **

```

2) 스위치 ADMIN 세팅 후 내부 Vlan 세팅
(아무 표시가 없는 곳은 Defult 세팅으로 그냥 엔터로 넘어가시면 됩니다.)

```
---- Basic System Configuration Dialog ----

This setup utility will guide you through the basic configuration of the system. Setup configures only enough connectivity for management of the system.

Please register Cisco Nexus 3000 Family devices promptly with your supplier.
Failure to register may affect response times for initial service calls.
Nexus devices must be registered to receive entitled support services.

Press Enter at anytime to skip a dialog. Use ctrl-c at anytime to skip the remaining dialogs.

** Would you like to enter the basic configuration dialog (yes/no): yes **

  Create another login account (yes/no) [n]:

  ** Configure read-only SNMP community string (yes/no) [n]: no **

  ** Configure read-write SNMP community string (yes/no) [n]: no **

  ** Enter the switch name : (switch 이름 설정) **

  ** Continue with Out-of-band (mgmt0) management configuration? (yes/no) [y]: **

    ** Mgmt0 IPv4 address : 192.168.0.250 **

    ** Mgmt0 IPv4 netmask : 255.255.255.0 **

** (스위치에 접속하기 위해 설정하는 것이지만 굳이 접속하지 않으려면 no로 넘어가도 괜찮음) **

  ** Configure the default gateway? (yes/no) [y]: **

    ** IPv4 address of the default gateway : 192.168.0.1 **

  Enable the telnet service? (yes/no) [n]:

  Enable the ssh service? (yes/no) [y]:

    ** Type of ssh key you would like to generate (dsa/rsa) : rsa **

    Number of  key bits <768-2048> :
    Warning: Incomplete configuration (ignored).

  Configure the ntp server? (yes/no) [n]:

  Configure default interface layer (L3/L2) [L2]:

  Configure default switchport interface state (shut/noshut) [noshut]:

  Configure CoPP System Policy Profile ( default / l2 / l3 ) [default]:

The following configuration will be applied:
  switchname nsr
interface mgmt0
ip address 192.168.0.250 255.255.255.0
no shutdown
exit
vrf context management
ip route 0.0.0.0/0 192.168.0.1
exit
  no telnet server enable
  system default switchport
  no system default switchport shutdown
  policy-map type control-plane copp-system-policy ( default )

Would you like to edit the configuration? (yes/no) [n]:

Use this configuration and save it? (yes/no) [y]:

[########################################] 100%
Copy complete, now saving to disk (please wait)…
```

3) 스위치의 전체적인 세팅 완료 후
```
User Access Verification
nsr login: admin
Password:
Cisco Nexus Operating System (NX-OS) Software
TAC support: http://www.cisco.com/tac
Copyright (c) 2002-2015, Cisco Systems, Inc. All rights reserved.
The copyrights to certain works contained in this software are
owned by other third parties and used and distributed under
license. Certain components of this software are licensed under
the GNU General Public License (GPL) version 2.0 or the GNU
Lesser General Public License (LGPL) Version 2.1. A copy of each
such license is available at
http://www.opensource.org/licenses/gpl-2.0.php and
http://www.opensource.org/licenses/lgpl-2.1.php
nsr# config
nsr(config)# show running-config

!Command: show running-config
!Time: Wed Dec  5 01:47:40 2018

version 6.0(2)U5(2)
switchname nsr

no feature telnet
feature lldp

username admin password 5 $1$XYUs3DQ7$apS0wc98dMn4zlkq5To6K0  role network-admin
ip domain-lookup
logging event link-status default
ip access-list copp-system-acl-eigrp
  10 permit eigrp any 224.0.0.10/32
ipv6 access-list copp-system-acl-eigrp6
  10 permit 88 any ff02::000a/128
ip access-list copp-system-acl-icmp
  10 permit icmp any any
ip access-list copp-system-acl-igmp
  10 permit igmp any any
ip access-list copp-system-acl-ntp
  10 permit udp any any eq ntp
  20 permit udp any eq ntp any
ip access-list copp-system-acl-pimreg
  10 permit pim any any
ip access-list copp-system-acl-ping
  10 permit icmp any any echo
  20 permit icmp any any echo-reply
ip access-list copp-system-acl-routingproto1
  10 permit tcp any gt 1024 any eq bgp
  20 permit tcp any eq bgp any gt 1024
  30 permit udp any 224.0.0.0/24 eq rip
  40 permit tcp any gt 1024 any eq 639
  50 permit tcp any eq 639 any gt 1024
  70 permit ospf any any
  80 permit ospf any 224.0.0.5/32
  90 permit ospf any 224.0.0.6/32
ip access-list copp-system-acl-routingproto2
  10 permit udp any 224.0.0.0/24 eq 1985
  20 permit 112 any 224.0.0.0/24
ip access-list copp-system-acl-snmp
  10 permit udp any any eq snmp
  20 permit udp any any eq snmptrap
ip access-list copp-system-acl-ssh
  10 permit tcp any any eq 22
  20 permit tcp any eq 22 any
  .....
  
  (세팅 목록 보여주기)
```

4) 추후 세팅

```
nsr# copy running-config startup-config
(설정 내역 저장)
```

