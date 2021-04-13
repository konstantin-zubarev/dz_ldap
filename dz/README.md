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

Необходимо произвести первоначальные настройки, указав имя FQDN хоста этого стенда. Без этого конфигурация сервера FreeIPA работать не будет.

```
[root@srv ~]# vi /etc/hosts

192.168.11.10    srv.local.lab    srv
```

Установим пакеты `ipa-server` и `ipa-server-dns` для настройки сервера FreeIPA. 
```
[root@srv ~]# yum install -y ipa-server ipa-server-dns
```

Настроим FreeIPA Server.
------------------------

Запустим конфигурацию центрального сервера индефикации FreeIPA.

```
[root@srv ~]# ipa-server-install
```

Настроим Firewalld.
-------------------

```
[root@srv ~]# systemctl enable --now firewalld
```
Настроим сервис firewalld

```
[root@srv ~]# firewall-cmd --permanent --zone=public --add-service={ldap,ldaps,dns}
[root@srv ~]# firewall-cmd --reload
[root@srv ~]# firewall-cmd --list-all --zone=public
```

Итак, настройка нашего центрального сервера идентификации FreeIPA успешно завершена.

Создать билет Kerberos.
-----------------------

Получим билет Kerberos с помощью команды `kinit admin`. Этот билет позволит нам использовать инструменты IPA.

```
[root@srv ~]# kinit admin
```

Панель управления FreeIPA.
--------------------------

В браузере перейдем по адресу https://rv.lab.local (user=admin; passwd=password). Необходимо прописать на хост машине в hosts адрес нашего сервера FreeIPA. 

```
192.168.11.10	srv.lab.local	srv
```





VLAN
----



![](1.jpg)

