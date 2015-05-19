#### По-долу е конфигурацията на суича Summit24, като съм поставил само фрагментите, които сме добавили допълнително в процеса на конфигурация
-----

```
# Създаваме VLAN-ите, които ще ползваме
# wan-initlab	(tag 3)		, интернета от initlab
# wan-tbc	(tag 6)		, интернета от tbc
# mgmt		(tag 2)		, това ни е вътрешната vlan-ка

create vlan "wan-initlab" 
create vlan "wan-tbc" 
create vlan "mgmt" 

#
# Config information for VLAN wan-initlab.
config vlan "wan-initlab" tag 3     # VLAN-ID=0x3  Global Tag 2
configure vlan "wan-initlab" add port 24 untagged
config vlan "wan-initlab" add port 25 tagged

#
# Config information for VLAN wan-tbc.
config vlan "wan-tbc" tag 6     # VLAN-ID=0x6  Global Tag 3
config vlan "wan-tbc" add port 24 tagged
config vlan "wan-tbc" add port 25 tagged


#
# Config information for VLAN mgmt.
config vlan "mgmt" tag 2     # VLAN-ID=0x2  Global Tag 4
config vlan "mgmt" ipaddress 10.1.2.2 255.255.255.0 
configure vlan "mgmt" add port 1 untagged
configure vlan "mgmt" add port 2 untagged
configure vlan "mgmt" add port 3 untagged
configure vlan "mgmt" add port 4 untagged
configure vlan "mgmt" add port 5 untagged
configure vlan "mgmt" add port 6 untagged
configure vlan "mgmt" add port 7 untagged
configure vlan "mgmt" add port 8 untagged
configure vlan "mgmt" add port 9 untagged
configure vlan "mgmt" add port 10 untagged
configure vlan "mgmt" add port 11 untagged
configure vlan "mgmt" add port 12 untagged
configure vlan "mgmt" add port 13 untagged
configure vlan "mgmt" add port 14 untagged
configure vlan "mgmt" add port 15 untagged
configure vlan "mgmt" add port 16 untagged
configure vlan "mgmt" add port 17 untagged
configure vlan "mgmt" add port 18 untagged
configure vlan "mgmt" add port 19 untagged
configure vlan "mgmt" add port 20 untagged
configure vlan "mgmt" add port 21 untagged
configure vlan "mgmt" add port 22 untagged
configure vlan "mgmt" add port 23 untagged
config vlan "mgmt" add port 25 tagged


# Това е description-а на интерфейсите
configure port 24 display-string "uplink"
configure port 25 display-string "alpha"

# Махаме присъствието на всички портове от default vlan-а
# което е по подразбиране
configure vlan default delete ports all
```
