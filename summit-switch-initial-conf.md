# Конфигурация на Summit Extreme switch

Summit Extreme е управляем мрежов комутатор (managed network switch). При тази инсталация свързва няколко различни мрежи към сървъра, като всяка от тях е настроена като виртуална (VLAN tagging), за да бъде изолирана от останалите.

Позволява управление през сериен порт, telnet и чрез web browser. Интерфейса за управление от команден ред (сериен/telnet) е без режими като всяка команда изрично указва управлявания обект и всички необходими параметри за промяната. Клавиш `Tab` допълва написаното и подсказва възможните команди, `Ctrl+H` трие последния символ, `Ctrl+W` последната дума, `Ctrl+U` целия ред, `Ctrl+A`/`Ctrl+E` е позициониране в началото/края на реда, `Ctrl+B`/`Ctrl+F` е предишен/следващ символ. Серийната връзка работи на `9600 8N1` без хардуерен flow control.

## Резюме на конфигурацията

Виртуални мрежи/VLAN-и:

  * VLAN 2 (mgmt) е за управление,
  * VLAN 3 (wan-initlab) е свързан към локалната мрежа на Инитлаб,
  * VLAN 6 (wan-tbc) е свързан към публичния Интернет през TBC.

Портове:

  * 1-23 за управление, VLAN 2 (mgmt) untagged,
  * 24 е свързан към мрежите на Инитлаб и TBC, VLAN 3 (wan-initlab) untagged, VLAN 6 (wan-tbc) tagged,
  * 25 е свързан към сървъра, и трите VLAN-а (2, 3, 6) са tagged.

Адрес за управление: `10.1.2.2` във VLAN 2 (mgmt).

## Първоначална конфигурация от команден ред

  * Изтриване на цялата текуща конфигурация:

        unconfigure switch all

  * Маха се VLAN-а по подразбиране `"Default"` от всички портове:

        configure vlan "Default" delete ports all

  * Надписват се портове 24 и 25:

        configure ports 24 display-string "uplink"
        configure ports 25 display-string "alpha"

  * Създава се VLAN 2 (mgmt) -- управление:

        create vlan "mgmt"
        configure vlan "mgmt" tag 2
        configure vlan "mgmt" ipaddress 10.1.2.2 255.255.255.0
        configure vlan "mgmt" add ports 1-23 untagged
        configure vlan "mgmt" add ports 25 tagged

  * Създава се VLAN 3 (wan-initlab) -- свързаност към локалната мрежа на Инитлаб:

        create vlan "wan-initlab"
        configure vlan "wan-initlab" tag 3
        configure vlan "wan-initlab" add ports 24 untagged
        configure vlan "wan-initlab" add ports 25 tagged

  * Създава се VLAN 6 (wan-tbc) -- свързаност към публичния Интернет през TBC:

        create vlan "wan-tbc"
        configure vlan "wan-tbc" tag 6
        configure vlan "wan-tbc" add ports 24 tagged
        configure vlan "wan-tbc" add ports 25 tagged

## Други често използвани команди

  * `show vlan` показва детайлен списък на всички конфигурирани VLAN-и, `show vlan "name"` за конкретен,
  * `show ports info`/`show port # info` показва детайлен списък на конфигурацията на портовете.

## За довършване

  * да се добави модела на устройството,
  * да се добави пълното ръководство за конфигурация към git,
  * как да се запази конфигурацията във флаша,
  * как да се качи по TFTP.
