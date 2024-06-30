## :rocket: Semana do MikroTik - TechLabs
:star_struck: Scripts Iniciais para os Roteadores.

### Antes de importar os scripts, HABILITE o IPv6!

---

#### Roteador Link 1:

```
/interface bridge add name=lo protocol-mode=none

/interface ethernet
set [ find default-name=ether1 ] comment=techlabs
set [ find default-name=ether2 ] comment=cdn
set [ find default-name=ether3 ] comment="link 2"

/interface vlan
add interface=ether2 name=VLAN14-CDN-V4 vlan-id=14
add interface=ether2 name=VLAN16-CDN-V6 vlan-id=16
add interface=ether3 name=VLAN24-TRANSITO-L2-V4 vlan-id=24
add interface=ether3 name=VLAN26-TRANSITO-L2-V6 vlan-id=26
add interface=ether1 name=VLAN584-CLI-TECHLABS-V4 vlan-id=584
add interface=ether1 name=VLAN586-CLI-TECHLABS-V6 vlan-id=586

/routing bgp instance set default as=100 redistribute-connected=yes redistribute-ospf=yes redistribute-other-bgp=yes redistribute-rip=yes redistribute-static=yes

/ip address
add address=172.16.0.1/30 interface=VLAN584-CLI-TECHLABS-V4 network=172.16.0.0
add address=172.16.0.5/30 interface=VLAN14-CDN-V4 network=172.16.0.4
add address=172.16.0.9/30 interface=VLAN24-TRANSITO-L2-V4 network=172.16.0.8
add address=200.200.0.1 interface=lo network=200.200.0.1
add address=8.8.8.8 interface=lo network=8.8.8.8

/ip route add distance=1 dst-address=200.200.0.0/22 type=blackhole

/ipv6 address
add address=fec0::1/126 advertise=no interface=VLAN586-CLI-TECHLABS-V6
add address=fec0::5/126 advertise=no interface=VLAN16-CDN-V6
add address=fec0::9/126 advertise=no interface=VLAN26-TRANSITO-L2-V6

/ipv6 route add distance=1 dst-address=fec0::/32 type=unreachable

/routing bgp network
add network=200.200.0.0/22
add network=fec0::/32

/routing bgp peer
add default-originate=always in-filter=PEER-CLI-TECHLABS-V4-IN name=PEER-CLI-TECHLABS-V4 out-filter=PEER-CLI-TECHLABS-V4-OUT remote-address=172.16.0.2 remote-as=250 update-source=VLAN584-CLI-TECHLABS-V4
add address-families=ipv6 default-originate=always in-filter=PEER-CLI-TECHLABS-V6-IN name=PEER-CLI-TECHLABS-V6 out-filter=PEER-CLI-TECHLABS-V6-OUT remote-address=fec0::2 remote-as=250 update-source=VLAN586-CLI-TECHLABS-V6
add in-filter=PEER-TRANSITO-IP-L2-V4-IN name=PEER-TRANSITO-IP-L2-V4 out-filter=PEER-TRANSITO-IP-L2-V4-OUT remote-address=172.16.0.10 remote-as=200
add address-families=ipv6 in-filter=PEER-TRANSITO-IP-L2-V6-IN name=PEER-TRANSITO-IP-L2-V6 out-filter=PEER-TRANSITO-IP-L2-V6-OUT remote-address=fec0::a remote-as=200
add in-filter=PEER-CDN-V4-IN name=PEER-CDN-V4 out-filter=PEER-CDN-V4-OUT remote-address=172.16.0.6 remote-as=300
add address-families=ipv6 in-filter=PEER-CDN-V6-IN name=PEER-CDN-V6 out-filter=PEER-CDN-V6-OUT remote-address=fec0::6 remote-as=300

/routing filter
add action=accept chain=PEER-CDN-V4-IN prefix=150.220.0.0/22 prefix-length=22-24
add action=discard chain=PEER-CDN-V4-IN
add action=accept chain=PEER-CLI-TECHLABS-V4-IN prefix=199.199.0.0/22 prefix-length=22-24
add action=discard chain=PEER-CLI-TECHLABS-V4-IN
add action=accept chain=PEER-CLI-TECHLABS-V4-OUT
add action=accept chain=PEER-CLI-TECHLABS-V6-IN prefix=fec0:3::/32 prefix-length=32-48
add action=discard chain=PEER-CLI-TECHLABS-V6-IN
add action=accept chain=PEER-CLI-TECHLABS-V6-OUT

/system identity set name=LINK-01

```

---

#### Roteador Link 2:

```
/interface bridge add name=loopback protocol-mode=none

/interface ethernet
set [ find default-name=ether1 ] comment=techlabs
set [ find default-name=ether2 ] comment="link 1"

/interface vlan
add interface=ether2 name=VLAN24-L1-V4 vlan-id=24
add interface=ether2 name=VLAN26-L1-V6 vlan-id=26
add interface=ether1 name=VLAN164-CLI-TECHLABS-V4 vlan-id=164
add interface=ether1 name=VLAN166-CLI-TECHLABS-V6 vlan-id=166

/routing bgp instance
set default as=200 redistribute-connected=yes redistribute-ospf=yes redistribute-other-bgp=yes redistribute-rip=yes redistribute-static=yes

/ip address
add address=172.16.0.10/30 interface=VLAN24-L1-V4 network=172.16.0.8
add address=172.17.0.1/30 interface=VLAN164-CLI-TECHLABS-V4 network=172.17.0.0
add address=1.1.1.1 interface=loopback network=1.1.1.1

/ip route add distance=1 dst-address=100.150.0.0/22 type=blackhole

/ipv6 address
add address=fec0::a/126 advertise=no interface=VLAN26-L1-V6
add address=fec0:1::1/126 advertise=no interface=VLAN166-CLI-TECHLABS-V6

/ipv6 route add distance=1 dst-address=fec0:1::/32 type=unreachable

/routing bgp network
add network=100.150.0.0/22
add network=fec0:1::/32

/routing bgp peer
add in-filter=PEER-TRANSITO-L1-V4-IN name=PEER-TRANSITO-L1-V4 out-filter= PEER-TRANSITO-L1-V4-OUT remote-address=172.16.0.9 remote-as=100
add address-families=ipv6 in-filter=PEER-TRANSITO-L1-V6-IN name=PEER-TRANSITO-L1-V6 out-filter=PEER-TRANSITO-L1-V6-OUT remote-address=fec0::9 remote-as=100
add default-originate=always in-filter=PEER-CLI-TECHLABS-V4-IN name=PEER-CLI-TECHLABS-V4 out-filter=PEER-CLI-TECHLABS-V4-OUT remote-address=172.17.0.2 remote-as=250
add address-families=ipv6 default-originate=always in-filter=PEER-CLI-TECHLABS-V6-IN name=PEER-CLI-TECHLABS-V6 out-filter=PEER-CLI-TECHLABS-V6-OUT remote-address=fec0:1::2 remote-as=250

/routing filter
add action=accept chain=PEER-CLI-TECHLABS-V4-IN prefix=199.199.0.0/22 prefix-length=22-24
add action=discard chain=PEER-CLI-TECHLABS-V4-IN
add action=accept chain=PEER-CLI-TECHLABS-V4-OUT
add action=accept chain=PEER-CLI-TECHLABS-V6-IN prefix=fec0:3::/32 prefix-length=32-48
add action=discard chain=PEER-CLI-TECHLABS-V6-IN
add action=accept chain=PEER-CLI-TECHLABS-V6-OUT

/system identity set name=LINK-02

```

--- 

#### Roteador CDN:
```
/interface bridge add name=loopback

/interface ethernet
set [ find default-name=ether1 ] comment=techlabs
set [ find default-name=ether2 ] comment="link 1"

/interface vlan
add interface=ether2 name=VLAN14-LINK1-V4 vlan-id=14
add interface=ether2 name=VLAN16-LINK1-V6 vlan-id=16
add interface=ether1 name=VLAN324-TECHLABS-V4 vlan-id=324
add interface=ether1 name=VLAN326-TECHLABS-V6 vlan-id=326

/routing bgp instance set default as=300

/ip address
add address=172.16.0.6/30 interface=VLAN14-LINK1-V4 network=172.16.0.4
add address=172.18.0.1/30 interface=VLAN324-TECHLABS-V4 network=172.18.0.0
add address=150.220.0.1 interface=loopback network=150.220.0.1

/ip route add distance=1 dst-address=150.220.0.0/22 type=blackhole

/ipv6 address
add address=fec0::6/126 advertise=no interface=VLAN16-LINK1-V6
add address=fec0:2::1/126 advertise=no interface=VLAN326-TECHLABS-V6

/ipv6 route add distance=1 dst-address=fec0:2::/32 type=unreachable

/routing bgp network
add network=fec0:2::/32
add network=150.220.0.0/22

/routing bgp peer
add in-filter=PEER-LINK1-V4-IN name=PEER-LINK1-V4 out-filter=PEER-LINK1-V4-OUT remote-address=172.16.0.5 remote-as=100
add address-families=ipv6 in-filter=PEER-LINK1-V6-IN name=PEER-LINK1-V6 out-filter=PEER-LINK1-V6-OUT remote-address=fec0::5 remote-as=100
add in-filter=PEER-TECHLABS-IN name=PEER-TECHLABS-V4 out-filter=PEER-TECHLABS-OUT remote-address=172.18.0.2 remote-as=250
add address-families=ipv6 in-filter=PEER-TECHLABS-V6-IN name=PEER-TECHLABS-V6 out-filter=PEER-TECHLABS-V6-OUT remote-address=fec0:2::2 remote-as=250

/routing filter add action=discard chain=PEER-TECHLABS-OUT

/system identity set name=CDN
```
