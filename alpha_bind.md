1. BIND config file
-----------------------
За конфигурационен файл използваме "named.conf.local", тъй като основния "named.conf" може
( и най-вероятно ще бъде) презаписан при update на BIND. Според Васил Колев, операционната 
система те пита дали искаш да ти презапише файла, но все пак е добре да се ползва named.conf.local
като safety measure. 

#### /etc/bind/named.conf.local
``` bash
	// isp.initlab.org е зоната, която ни е делегирана от Васил Колев
	// Също така в DNS-а на initlab.org има настроени GLUE записи, 
	// които връщат "А" записи за [ns1|ns2].isp.initlab.org
	//
	zone "isp.initlab.org" {
	        type master;
	        file "/etc/bind/isp.initlab.org";
	        allow-update {none;};
	        allow-transfer { 188.166.63.31; };	// Това е slave сървър, администриран от Васил Илиев и ще се ползва като redundancy
	};

	
	// Обратни PTR записи за мрежа:
	// 10.1.2.0/24 
	zone "0.2.1.10.in-addr.arpa" {
	        type master;
	        file "/etc/bind/2.1.10.in-addr.arpa";
	};
```
-----------------------


2. BIND options file
----------

####/etc/bind/named.conf.options
-----------------------
```bash
	options {
        directory "/var/cache/bind";

        auth-nxdomain no;    		# conform to RFC1035
        listen-on       { 127.0.0.1; 94.26.8.55; 10.1.2.1; };
        listen-on-v6 { any; };

	# allow-recursion, ограничава рекурсията само за контролирани от нас мрежи
	# Това е добре да го има, тъй като в противен случай, нейм сървъра може
	# да се ползва за атаки към трети лица
        allow-recursion { 127.0.0.1; 10.0.0.0/8; };


        notify yes;

	# Позволява трансфер на зоните за slave dns-а
        allow-transfer { 188.166.63.31; };

        dnssec-enable no;

	# Това е версията, която BIND ще връща като версия на Software-а
	# Използва се за маскиране с цел предпазване от атаки, при които
	# знаейки версията можеш да пробваш конкретен exploit за нея
        version "alpha";
	};
```
-----------------------


3. Зонови файлове за  isp.initlab.org и 10.1.2.0/24 
-----------------------
Важно е да имаме настроен достатъчно нисък TTL, така че ако по някаква причина
се наложи промяна на някой запис, това да стане възможно най-бързо

#### /etc/bind/isp.initlab.org
-----------------------
```bash
	$ORIGIN .
	$TTL 300       ; 5 minuti
	isp.initlab.org         IN SOA  ns1.isp.initlab.org root.isp.initlab.org. (
	                                2015051602 ; serial
	                                10800      ; refresh (3 hours)
	                                3600       ; retry (1 hour)
	                                1209600    ; expire (2 weeks)
	                                10800      ; minimum (3 hours)
	                                )
	                        NS      ns1.isp.initlab.org.
	                        NS      ns2.isp.initlab.org.
	                        A       94.26.8.55
	
	$ORIGIN isp.initlab.org.		
	$TTL 300
	NS1     A       94.26.8.55
	NS2     A       188.166.63.31
```
-----------------------


#### Зонов файл за обратни записи на 10.1.2.0/24
#### /etc/bind/2.1.10.in-addr.arpa  
-----------------------
```bash
	;
	; BIND reverse data file for broadcast zone
	;
	$ORIGIN 0.2.1.10.in-addr.arpa.
	$TTL    300
	@       IN      SOA     ns1.isp.initlab.org. root.isp.initlab.org. (
	                    2015051604          ; Serial
	                         604800         ; Refresh
	                          86400         ; Retry
	                        2419200         ; Expire
	                         604800 )       ; Negative Cache TTL
	;
	2.1.10.in-addr.arpa.    IN      NS      ns1.isp.initlab.org.
	2.1.10.in-addr.arpa.    IN      NS      ns2.isp.initlab.org.
	
	1.2.1.10        PTR     alpha.
```
-----------------------

