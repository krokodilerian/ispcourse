# Конфигурация на Cisco 2600

Според Cisco 2600 е многофункционален рутер.

Позволява управление през сериен порт. Интерфейса за управление от команден ред е с режими, от които зависи кои команди ще бъдат приети; режимите могат да се разглеждат подобно на директории, в/от които може да се влиза/излиза. Клавиш `Tab` допълва написаното. Серийната връзка работи на `9600 8N1` без хардуерен flow control.

## Резюме на конфигурацията

Рутера свързва серийна линия (`Serial1/0`, PPP през `line 33`) със сървър beta, който му е настроен като default gateway. Реално през тази връзка идва интернета към рутера. 

Локална мрежа (за клиенти) - Това е интерфейса на рутера: `Ethernet0/0`, който раздава DHCP адреси от мрежа 10.2.0.0/24 и влиза в суича към порт от VLAN 50. 

Има конфигуриран DNS resolver и NTP сървър, които са настроени да са 10.1.2.1 (ip-то на BETA). 

IP-то на серийния интерфейс му го дава `beta`, коят му пушва 10.1.3.2



## За довършване

  * да се добави пълното ръководство за конфигурация към git.


##Често използвани команди

###### Запомняне на текущата конфигурация
client-router(config)# copy running-config startup-config
или
client-router(config)# wr

###### Качване на конфигурацията през TFTP (в режим `enable`):
client-router(config)# copy running-config tftp://10.1.2.1/cisco2600-20150516-v1.conf

###### Changing hostname 
client-router(config)hostname client-router

###### Configure password
client-router(config)enable secret zoo3aiH;u0xe=imooch9

###### Configure vty password
client-router(config)# line vty 0 4
client-router(config-line)# password  zoo3aiH;u0xe=imooch9
client-router(config-line)# login

###### Configure console password 
client-router(config)# line console 0
client-router(config-line)#password 0 zoo3aiH;u0xe=imooch9

###### Configure password encryption
client-router#config t
client-router(config)#service password-encryption 


###### Настройка на DNS резолвер
client-router(config)#ip name-server 10.1.2.1

###### Настройка на default domain
client-router(config)#ip domain-name isp.initlab.org

###### NTP сървър настройка
client-router(config)#clock timezone PST -8
client-router(config)#ntp server 10.1.2.1

###### Проверка на дата/час
client-router#show clock

###### Set interface defaults on s1/7 - Рестартира конфигурацията на интефейса 
client-router(config)# default interface s1/7


###### Configure interface description
client-router(config)# int s1/0
client-router(config-if)#description uplink-to-beta    

###### Disable domain ip lookup (изнервящото lookup-ване като въведеш грешна команда и нямаш dsn resolver) 
client-router(config)# no ip domain-lookup

###### Disable console logging (досадни съобщения за вдигане на интерфейси)
client-router(config)# no logging console
