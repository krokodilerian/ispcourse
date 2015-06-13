# Конфигурация на Summit Extreme switch

ExtremeWare Summit24 е управляем мрежов комутатор (managed network switch). При тази инсталация свързва няколко различни мрежи към сървъра, като всяка от тях е настроена като виртуална (VLAN tagging), за да бъде изолирана от останалите.

Позволява управление през сериен порт, telnet и чрез web browser. Интерфейса за управление от команден ред (сериен/telnet) е без режими като всяка команда изрично указва управлявания обект и всички необходими параметри за промяната. Клавиш `Tab` допълва написаното и подсказва възможните команди, `Ctrl+H` трие последния символ, `Ctrl+W` последната дума, `Ctrl+U` целия ред, `Ctrl+A`/`Ctrl+E` е позициониране в началото/края на реда, `Ctrl+B`/`Ctrl+F` е предишен/следващ символ. Серийната връзка работи на `9600 8N1` без хардуерен flow control.

## Резюме на конфигурацията

Виртуални мрежи/VLAN-и:

  * VLAN 2 (mgmt) е за управление,
  * VLAN 3 (wan-initlab) е свързан към локалната мрежа на Инитлаб,
  * VLAN 6 (wan-tbc) е свързан към публичния Интернет през TBC,
  * VLAN 50 (customers_50) е излиза в лекционната зала на InitLab, където е свързан WAP nineties,
  * VLAN 1024 (core) за опорно оборудване.

Портове:

  * 1 е свързан към сървъра (`10.1.2.1`), и трите VLAN-а (2, 3, 6) са tagged,
  * 2 е свързан към "клиента" (Cisco 2500, `10.1.3.2`) във VLAN 50 (customers_50), untagged,
  * 3 е свързан към `accessrouter` във VLAN 1024 (core) tagged,
  * 4 е свързан към втория сървър, gamma (`10.1.2.3`), VLAN-и 2, 3, 1024 tagged,
  * 6 e NetControl устройство (`10.1.2.5`) през VLAN 2 (mgmt),
  * 25 е свързан към мрежите на Инитлаб и TBC, VLAN 3 (wan-initlab) untagged, VLAN 6 (wan-tbc) tagged, VLAN 50 (customers_50) tagged,
  * останалите са за управление, VLAN 2 (mgmt) untagged.

Адрес за управление: `10.1.2.2` във VLAN 2 (mgmt).

## Първоначална конфигурация от команден ред

  * Изтриване на цялата текуща конфигурация:

        unconfigure switch all

  * Синхронизация на времето по NTP през beta:

        configure timezone +120 autodst
        enable sntp-client
        configure sntp-client primary server 10.1.2.1
        configure sntp-client update-interval 60

  * DNS през beta:

        configure dns-client default-domain isp.initlab.org
        configure dns-client add 10.1.2.1

  * Информация за устройството:

        configure snmp sysName CoreSwitch
        configure snmp sysLocation entranceRack.initlab.org
        configure snmp sysContact "root@isp.initlab.org"

  * Маха се VLAN-а по подразбиране `"Default"` от всички портове:

        configure vlan "Default" delete ports all

  * Надписват се портовете:

        configure ports 1 display-string "1-beta"
        configure ports 2 display-string "2-client"
        configure ports 3 display-string "3-accessrouter"
        configure ports 4 display-string "4-gamma"
        configure ports 6 display-string "6-netcontrol"
        configure ports 25 display-string "25-InitLab"

  * Създава се VLAN 2 (mgmt) -- управление:

        create vlan "mgmt"
        configure vlan "mgmt" tag 2
        configure vlan "mgmt" ipaddress 10.1.2.2 255.255.255.0
        configure vlan "mgmt" add ports 1 tagged
        configure vlan "mgmt" add ports 4 tagged
        configure vlan "mgmt" add ports 5-24 untagged

  * Създава се VLAN 3 (wan-initlab) -- свързаност към локалната мрежа на Инитлаб:

        create vlan "wan-initlab"
        configure vlan "wan-initlab" tag 3
        configure vlan "wan-initlab" add ports 1 tagged
        configure vlan "wan-initlab" add ports 4 tagged
        configure vlan "wan-initlab" add ports 25 untagged

  * Създава се VLAN 6 (wan-tbc) -- свързаност към публичния Интернет през TBC:

        create vlan "wan-tbc"
        configure vlan "wan-tbc" tag 6
        configure vlan "wan-tbc" add ports 1 tagged
        configure vlan "wan-tbc" add ports 25 tagged

  * VLAN 50 (customers_50) свързва Cisco 2500 със лекционната зала на InitLab, там е WAP nineties:

        create vlan "customers_50"
        configure vlan "customers_50" tag 50
        configure vlan "customers_50" add ports 2 untagged
        configure vlan "customers_50" add ports 25 tagged

  * VLAN 1024 (core) за опорно оборудване:

        create vlan "core"
        configure vlan "core" tag 1024
        configure vlan "core" add ports 1 tagged
        configure vlan "core" add ports 3 untagged
        configure vlan "core" add ports 4 tagged

## Други често използвани команди

  * `show vlan` показва детайлен списък на всички конфигурирани VLAN-и, `show vlan "name"` за конкретен,
  * `show ports info`/`show port # info` показва детайлен списък на конфигурацията на портовете.
  * `save configuration` за запазване на промените по конфигурацията,
  * `upload configuration 10.1.2.1 summit-20150606.conf` за качване на конфигурацията по TFTP ~~(файла трябва да се създаде (примерно с `touch`) в `/srv/tftp` и да се дадат права за писане на всички -- `0666`)~~.
