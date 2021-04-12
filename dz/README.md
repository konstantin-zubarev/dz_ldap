Стенд. Централизованная авторизация и аутентификация. LDAP.
===========================================================

Цель:

1. Установить FreeIPA;
2. Написать Ansible playbook для конфигурации клиента;
3. Настроить аутентификацию по SSH-ключам; 
4. Firewall должен быть включен на сервере и на клиенте.

Для демонстрации этого стенда, использую CentOS 7. Ниже приведены сведения о хостах.


|             |        SRV        |        PC1        |        PC2        |
|-------------|:-----------------:|:-----------------:|:-----------------:|
| IP          | 192.168.1.10/24   | 192.168.1.20/24   | 192.168.1.21/24   |
| FQDN        | srv.local.lab     | pc1.local.lab     | pc2.local.lab     |


Реализация.
-----------
![](topology.jpeg)

Установите FreeIPA Server.
--------------------------

Настроим файл hosts, указав имя FQDN хоста этого стенда. Без этого конфигурация сервера FreeIPA работать не будет.

```
[root@srv ~]# vi /etc/hosts

192.168.11.10    srv.local.lab    srv
```

Для настройки сервера FreeIPA, уснановим `ipa-server` и `ipa-server-dns`
```
[root@srv ~]# yum install -y ipa-server ipa-server-dns
```

Настроим FreeIPA Server.
------------------------

Запустим конфигурацию центрального сервера индефикации FreeIPA.

```
[root@srv ~]# ipa-server-install
```

Итак, настройка нашего центрального сервера идентификации FreeIPA успешно завершена.

Создать билет Kerberos.
-----------------------

Получим билет Kerberos с помощью команды `kinit admin`. Этот билет позволит нам использовать инструменты IPA.

```

```





VLAN
----

Настроим vlan интерфейсы testClient1 <-> testServer1 testClient2 <-> testServer2.

<details>
  <summary>testClient1</summary>

```
[root@testClient1 ~]# cat /etc/sysconfig/network-scripts/ifcfg-vlan10

ONBOOT=yes
TYPE=Vlan
VLAN=yes
VLAN_NAME_TYPE=VLAN_PLUS_VID_NO_PAD
DEVICE=vlan10
PHYSDEV=eth1
VLAN_ID=10
BOOTPROTO=static
IPADDR=10.10.10.254
NETMASK=255.255.255.0
```
</details>

<details>
  <summary>testServer1</summary>

```
[root@testServer1 ~]# cat /etc/sysconfig/network-scripts/ifcfg-vlan10

ONBOOT=yes
TYPE=Vlan
VLAN=yes
VLAN_NAME_TYPE=VLAN_PLUS_VID_NO_PAD
DEVICE=vlan10
PHYSDEV=eth1
VLAN_ID=10
BOOTPROTO=static
IPADDR=10.10.10.1
NETMASK=255.255.255.0

```
</details>

<details>
  <summary>testClient2</summary>

```
[root@testClient2 ~]# cat /etc/sysconfig/network-scripts/ifcfg-vlan20 

ONBOOT=yes
TYPE=Vlan
VLAN=yes
VLAN_NAME_TYPE=VLAN_PLUS_VID_NO_PAD
DEVICE=vlan20
PHYSDEV=eth1
VLAN_ID=20
BOOTPROTO=static
IPADDR=10.10.10.254
NETMASK=255.255.255.0
```
</details>

<details>
  <summary>testServer2</summary>

```
[root@testServer2 ~]# cat /etc/sysconfig/network-scripts/ifcfg-vlan20

ONBOOT=yes
TYPE=Vlan
VLAN=yes
VLAN_NAME_TYPE=VLAN_PLUS_VID_NO_PAD
DEVICE=vlan20
PHYSDEV=eth1
VLAN_ID=20
BOOTPROTO=static
IPADDR=10.10.10.1
NETMASK=255.255.255.0
```
</details>

Разделенная локальная сеть, не конфлектует между собой.

LACP
----

Для проверки отключим один из интерфейсов и убедимся, что доступ к хосту не изменилась.

![](1.jpg)

