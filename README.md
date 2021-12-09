Muh Hilmy Thoriq YR (05111942000012)<br>
Salsabila Irbah (05111942000007)<br>
Nadhif Bhagawanta Hadiprayitno (05111942000029)<br>

# ANSWER
# Problem A,B,C
First We need to make topology and do subnetting and routing<br>
Here we're using CIDR<br>
![Topology 1](https://user-images.githubusercontent.com/81411468/145398487-d9500632-c9de-4790-8f31-81d74e483a35.PNG)
![Topology 2](https://user-images.githubusercontent.com/81411468/145398494-a80a2c0d-00d3-4606-b3eb-2dba0be862c2.PNG)
![Topology 3](https://user-images.githubusercontent.com/81411468/145398499-4703017e-68d1-46fa-a423-fc5db29d2124.PNG)
![Topology 4](https://user-images.githubusercontent.com/81411468/145398507-00e27c75-364f-484a-ab34-5f84ef1bde99.PNG)<br>

After that we can make the ip distribution looks like this based on our IP Tree<br>
![Pohon IP](https://user-images.githubusercontent.com/81411468/145398755-3d284992-4602-4fe8-8451-fb89e8601c47.PNG)
![Pembagian IP](https://user-images.githubusercontent.com/81411468/145398698-733973a1-3854-4389-9828-92c807b417fc.PNG)<br>

For the routing, we can setting our `foosha` so the other router can connect to the internet
```
route add -net 192.210.0.0 netmask 255.255.240.0 gw 192.210.4.2
route add -net 192.210.16.0 netmask 255.255.248.0 gw 192.210.21.2
```

# Problem D
Next we need to set our client to dynamic ip using dhcp server and the router that connected are the dhcp relay
```
auto eth0
iface eth0 inet dhcp
```

Then we need to setting in `/etc/default/isc-dhcp-server` at `Jipangu`
```
INTERFACES="eth0"
```

and setting in `/etc/default/isc-dhcp-relay` at both `Water7` and `Guanhao`
```
SERVERS="192.210.8.131"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=""
```

Finally we need to set our 4 subnet in `Jipangu` by editing `/etc/dhcp/dhcpd.conf`
```
subnet 192.210.8.0 netmask 255.255.255.128 {
    range 192.210.8.1 192.210.8.126;
    option routers 192.210.8.1;
    option broadcast-address 192.210.8.127;
    option domain-name-servers 192.210.8.130;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 192.210.0.0 netmask 255.255.252.0 {
    range 192.210.0.1 192.210.3.254;
    option routers 192.210.0.1;
    option broadcast-address 192.210.3.255;
    option domain-name-servers 192.210.8.130;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 192.210.16.0 netmask 255.255.254.0 {
    range 192.210.16.1 192.210.17.254;
    option routers 192.210.16.1;
    option broadcast-address 192.210.17.255;
    option domain-name-servers 192.210.8.130;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 192.210.20.0 netmask 255.255.255.0 {
    range 192.210.20.1 192.210.20.254;
    option routers 192.210.20.1;
    option broadcast-address 192.210.20.255;
    option domain-name-servers 192.210.8.130;
    default-lease-time 360;
    max-lease-time 7200;
}
subnet 192.210.8.128 netmask 255.255.255.248 {
        option routers 192.210.8.131;
}
```
*TESTING*<br>
**BLUENO**<br>
![BLUENO](https://user-images.githubusercontent.com/81411468/145400481-6cfdc5b3-a598-4729-b975-cdcb0473a664.PNG)<br>
**CIPHER**<br>
![CIPHER](https://user-images.githubusercontent.com/81411468/145400489-8b5fd97d-a384-41bd-8107-5aa2e0cc84b0.PNG)<br>
**ELENA**<br>
![ELENA](https://user-images.githubusercontent.com/81411468/145400500-570fecdf-415e-4abd-8b2a-fe79b83ff1da.PNG)<br>
**FUKUROU**<br>
![FUKUROU](https://user-images.githubusercontent.com/81411468/145400517-a5626237-e35d-4434-9016-496eec9b1729.PNG)
<br>

*Difficulties*
- No Problem 

# Problem D1
To make our Topology can access to outside we will using this
```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source <ip foosha>
```
Because `foosha` using dynamic ip we need to change the ip each time we're re-open our GNS file

*Difficulties*
- Must Change ip foosha everytime re-open the project

# Problem D2
To Make Our DHCP Server and DNS server ( `Jipangu` and `Doriki` ) drop `HTTP` access we're using this in `Water7`
```
iptables -A FORWARD -d 192.210.8.131 -p tcp --dport 80 -j DROP
iptables -A FORWARD -d 192.210.8.130 -p tcp --dport 80 -j DROP

```
*Testing*<br>
**DHCP**<br>
![Testing D2](https://user-images.githubusercontent.com/81411468/145401699-36bf9295-bc36-4410-a9eb-2efb234ac55e.PNG)<br>
**DNS**<br>
![Testing D2 1](https://user-images.githubusercontent.com/81411468/145401738-10f82d60-8bb0-4a92-92dc-25370b8ad189.PNG)<br>

*Difficulties*
- No Problem

# Problem D3
We want to Limit our icmp Connection into 3 that going to DHCP and DNS Server, we're using this in `Water7`
```
iptables -A FORWARD -d 192.210.8.130 -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
iptables -A FORWARD -d 192.210.8.131 -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

*Testing*
![Revisi_Modul5_IUP4_Fix_ - Google Chrome 2021-12-09 20-10-29](https://user-images.githubusercontent.com/81411468/145405298-00e4df2a-104f-48c5-ad80-06a74ad31e10.gif)

*Diffuculties*
- No Problem

# Problem D4
We want to make `Blueno` and `Cipher` Can only be used in 07.00-15.00 at Monday to Thursday Only that access `Doriki`<br>
We need to Set `iptables` in both client using
```
iptables -A OUTPUT -d 192.210.8.130 -m time --timestart 06:59 --timestop 15:01 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A OUTPUT -d 192.210.8.130 -j REJECT
```

*Testing*<br>
`Blueno` Thursday 8:00<br>
![Blueno Kamis jam 8](https://user-images.githubusercontent.com/81411468/145407272-c2476894-f34b-4d8e-b1d2-e0cce59f0f66.PNG)<br>
`Blueno` Thursday 21:00<br>
![Blueno kamis jam 9](https://user-images.githubusercontent.com/81411468/145407350-e612eccc-d655-4821-834a-696de9a21c1e.PNG)<br>
`Blueno` Friday 8:00<br>
![Blueno Jumat jam 8](https://user-images.githubusercontent.com/81411468/145407415-b4abd56a-e5bc-4f48-8f9a-ebb6c16c7c71.PNG)<br>

`Cipher` Thursday 8:00<br>
![Cipher kamis jam 8](https://user-images.githubusercontent.com/81411468/145407505-0bd17200-7496-484b-bf7f-92da5ddaf03c.PNG)<br>
`Cipher` Thursday 21:00<br>
![Cipher Kamis jam 9](https://user-images.githubusercontent.com/81411468/145407575-303c4102-4a11-4df8-bc09-6ad59057dcdc.PNG)<br>
`Cipher` Friday 8:00<br>
![Cipher Jumat jam 8](https://user-images.githubusercontent.com/81411468/145407590-3acb056c-9295-4456-b015-4baae964c330.PNG)<br>

*Difficulties*
- No Problem

# Problem D5
We want to make `Elena` and `Fukurou` can only be used in 15.01-06.59 that access `Doriki`<br>
We need to Set `iptables` in both client using
```
iptables -A OUTPUT -d 192.210.8.130 -m time --timestart 15:00 --timestop 07:00 -j ACCEPT
iptables -A OUTPUT -d 192.210.8.130 -j REJECT

```

*Testing*<br>
`Elena` 8:00<br>
![Elena Jam 8](https://user-images.githubusercontent.com/81411468/145407722-633d5163-b0c4-4846-bb9b-29238c7b7f85.PNG)<br>
`Elena` 19:00<br>
![Elena Jam 19](https://user-images.githubusercontent.com/81411468/145407756-f806dca6-2a72-4989-97c5-5bc0e05aebee.PNG)<br>
`Fukurou` 8:00<br>
![Fukurou jam 8](https://user-images.githubusercontent.com/81411468/145407872-bfbe51bc-943a-4016-9551-c137943444f8.PNG)<br>
`Fukurou` 19:00<br>
![Fukurou jam 19](https://user-images.githubusercontent.com/81411468/145407887-62861175-28f8-4aca-9123-2dd58b212ce8.PNG)<br>

*Difficulties*
- No Problem

# Problem D6
We want to change the destination if we access `Doriki` it will redirect to `Jorge` and `Maingate` consecutively
For `Elena` and `Fukurou` we can setting the `iptables` in `Guanhao` using
```
iptables -t nat -p tcp -A PREROUTING -d 192.210.8.130 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.210.18.2:80
iptables -t nat -p tcp -A PREROUTING -d 192.210.8.130 -j DNAT --to-destination 192.210.18.3:80
```
and for `Blueno` and `Cipher` we can setting the `iptables` in `Water7` using
```
iptables -t nat -p tcp -A PREROUTING -d 192.210.8.130 -m statistic --mode nth --every 2 --packet 0  -j DNAT --to-destination 192.210.18.2:80 
iptables -t nat -p tcp -A PREROUTING -d 192.210.8.130 -j DNAT --to-destination 192.210.18.3:80 
```

*Testing*

*Difficulties*
- Can't only setting in `Guanhao` for `Blueno` and `Cipher`
- Hard to figure out 
- Don't know how to testing

