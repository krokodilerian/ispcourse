#### Това е конфигурацията на рутера, която ръчно сме променяли на 16.05.2015

------------------------------------------------------
```

! Махаме ip-то на рутера от DHCP pool-а
! за да не го даде на клиент
ip dhcp excluded-address 10.2.0.1
! 
! Създаваме отделен dhcp поол
ip dhcp pool ll-client1
	! Дефинираме мрежата, от която ще раздаваме адреси
   network 10.2.0.0 255.255.255.0
	! default-router е default-gateway
   default-router 10.2.0.1 
	! dns-server би трябвало да е резолвера, който ще дадем
   dns-server 10.1.2.1 

   domain-name ll-client1.foo
!
ip dhcp-server 10.2.0.1
!
!
interface Ethernet0/0
 ip address 10.2.0.1 255.255.255.0
 no ip directed-broadcast
!
interface Serial1/0
 physical-layer async
 ip address negotiated
 no ip directed-broadcast
 encapsulation ppp
 async dynamic address
 async mode dedicated
 fair-queue 64 16 0
 ppp ipcp accept-address
!
interface Serial1/7
 bandwidth 2048
 ip address 10.0.0.2 255.255.255.0
 no ip directed-broadcast
 fair-queue 64 32 0
!
! ip-default-gateway-а май е излишен
! при положение, че добавихме статичен route 
! но ще трябва да го тестваме
ip default-gateway 172.31.190.1
ip classless
! Default route, така че всичко да се рутира през интерфейса към Alpha
ip route 0.0.0.0 0.0.0.0 Serial1/0
line 1
 autoselect ppp
 modem DTR-active
 stopbits 1
 speed 115200
end
```
 
