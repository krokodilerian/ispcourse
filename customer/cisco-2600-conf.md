# Конфигурация на Cisco 2600

Според Cisco 2600 е многофункционален рутер.

Позволява управление през сериен порт. Интерфейса за управление от команден ред е с режими, от които зависи кои команди ще бъдат приети; режимите могат да се разглеждат подобно на директории, в/от които може да се влиза/излиза. Клавиш `Tab` допълва написаното. Серийната връзка работи на `9600 8N1` без хардуерен flow control.

## Резюме на конфигурацията

Рутера свързва серийна линия (`Serial1/0`, PPP през `line 33`) с локална Ethernet мрежа (`Ethernet0/0`), като работи и като DHCP сървър за мрежата `10.2.0.0/24`.

## Първоначална конфигурация от команден ред

Командите са изброени йерархично, като отместването означава, че предходната команда променя режима. За връщане в предишен режим (директория) се използва `exit`.

	enable
		configure terminal
			interface Ethernet0/0
				ip address 10.2.0.1 255.255.255.0
	
			service dhcp
			ip dhcp excluded-address 10.2.0.1
			ip dhcp pool ll-client1
				network 10.2.0.0 /24
				default-router 10.2.0.1
				dns-server 10.1.2.1
				domain-name ll-client1.foo
	
			interface Serial1/0
				physical-layer async
				async mode dedicated
				encapsulation ppp
				async dynamic address
				ppp ipcp accept-address
				ip address negotiated
	
			line 33
				speed 115200
				stopbits 1
	
			ip route 0.0.0.0 0.0.0.0 Serial1/0

Запомняне на конфигурацията (в режим `enable`):

	copy running-config startup-config

Качване на конфигурацията през TFTP (в режим `enable`):

	copy running-config tftp://10.1.2.1/cisco2600-20150516-v1.conf

## За довършване

  * да се добави пълното ръководство за конфигурация към git.
