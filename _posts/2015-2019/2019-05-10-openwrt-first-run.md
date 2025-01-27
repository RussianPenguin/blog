---
layout: post
title: 'OpenWRT: Настройка после установки'
type: post
categories:
- HowTo
tags:
- linux
- openwrt
permalink: "/2019/05/10/openwrt-%d0%bd%d0%b0%d1%81%d1%82%d1%80%d0%be%d0%b9%d0%ba%d0%b0-%d0%bf%d0%be%d1%81%d0%bb%d0%b5-%d1%83%d1%81%d1%82%d0%b0%d0%bd%d0%be%d0%b2%d0%ba%d0%b8/"
excerpt: Небольшой скетч про послеустановочную настройку OpenWRT.
---
![openwrt]({{ site.baseurl }}/assets/images/2019/05/openwrt.png){:.img-fluid}

Небольшой скетч про послеустановочную настройку OpenWRT.

**Настройка флешки как overlayfs**

Большинство роутеров имеют у себя usb-порт куда можно воткнуть флешку и использовать ее для swap-файла (вдруг что) и для overlayfs. [https://wiki.openwrt.org/ru/doc/howto/extroot](https://wiki.openwrt.org/ru/doc/howto/extroot)

**Настройка dropbear**

_/etc/config/dropbear_

```
config dropbear  
 option PasswordAuth 'on'  
 option RootPasswordAuth 'on'  
 option Port '22'  
 # option BannerFile '/etc/banner'  
 option Interface 'lan'  

```

**Выставляем ssh только в lan-интерфейс**

[https://wiki.openwrt.org/doc/howto/secure.access](https://wiki.openwrt.org/doc/howto/secure.access)

**Добавление пользователя**

```
# opkg update  
# opkg install shadow-usermod shadow-useradd shadow-groupadd
```

```
# mkdir /home  
# useradd -m -s /bin/ash penguin  
# passwd penguin  
# opkg update  
# opkg install sudo  
# groupadd --system sudo  
# usermod -a -G sudo penguin  
# visudo
```

Добавляем строки, которые позволяют группе sudo использовать sudo

```shell
%sudo ALL=(ALL) ALL
```

**Настройка dropbear для запрета доступа root**

На локальной машине

```shell
$ ssh-copy-id 
```

На сервере в конфиге dropbear

```
  
 option PasswordAuth 'off'  
 option RootPasswordAuth 'off'  

```

Предварительно убеждаемся что ключи работают. Иначе останетесь без доступа.

