---
layout: post
title: Как организовать собственную доменную зону (windows)
type: post
categories:
- HowTo
tags:
- администрирование
permalink: "/2013/11/15/%d1%81%d0%be%d0%b1%d1%81%d1%82%d0%b2%d0%b5%d0%bd%d0%bd%d0%b0%d1%8f_%d0%b4%d0%be%d0%bc%d0%b5%d0%bd%d0%bd%d0%b0%d1%8f_%d0%b7%d0%be%d0%bd%d0%b0/"
---
Для организации собственных доменных зон первого уровня в windows стоит использовать deadwood+maradns. За ссылками в гугль.  
Первый - это кеширующий dns, который позволяет добавлять кастомные рут-сервера для определенных зон. А второй - нерекурсивный днс для организации сервера кастомной зоны.  
<!--more-->  
Примеры конфигов:

**deadwood**

```
# Lines with a '#' at the beginning are comments ignored by Deadwood's
# text file parser.
# If you wish to use this program to cache from other recursive servers
# instead of doing its own recursion, uncomment the following lines 
# # Please note that each upstream_servers entry takes up space in Deadwood's 
# cache and that maximum_cache_elements will need to be increased to store 
# a large number of these entries. 
upstream_servers = {} 
upstream_servers["."]="172.16.0.1, 8.8.8.8, 8.8.4.4" 
# Servers we connect to # It is also possible to use other root servers or to blacklist 
# a phising website. However, to do this, root_servers needs to be 
# defined. For example, to blacklist the domain "phish.example.com": 
#root_servers = {} 
# ICANN DNS root servers (Deadwood default if both root_servers and 
# upstream_servers are not defined) 
#root_servers["."]="198.41.0.4, 192.228.79.201, 192.33.4.12, 128.8.10.90, " 
#root_servers["."]+="192.203.230.10, 192.5.5.241, 192.112.36.4, 128.63.2.53, " 
#root_servers["."]+="192.36.148.17, 192.58.128.30, 193.0.14.129, 199.7.83.42, " 
#root_servers["."]+="202.12.27.33" 
#root_servers["phish.example.com."]="10.254.254.254" 
# Please note that each root_servers entry takes up space in Deadwood's 
# cache and that maximum_cache_elements will need to be increased to store 
# a large number of these entries. # The IP this program has bind_address="127.0.0.1" 
# The IPs allowed to connect and use the cache recursive_acl = "127.0.0.1/16" 
# The file containing a hard-to-guess secret random_seed_file = "secret.txt" 
# This is the file Deadwood uses to read the cache to and from disk cache_file = "dw_cache_bin" 
# This is a list of IPs that, if we get them in a DNS reply, we convert 
# the reply in to a "not there" answer. 
#ip_blacklist = "10.222.33.44, 10.222.3.55" 
# By default, for security reasons, Deadwood does not allow IPs in the 
# 192.168.x.x, 172.[16-31].x.x, 10.x.x.x, 127.x.x.x, 169.254.x.x, 
# 224.x.x.x, or 0.0.x.x range. If using Deadwood to resolve names 
# on an internal network, uncomment the following line: filter_rfc1918 = 0 root_servers = {} 
# our custom zone .some - root server 
root_servers["some."]="172.16.1.1" 
#root_servers["example.com."]="172.16.1.1"
```

**MaraDNS**

```
ipv4_bind_addresses = "172.16.1.1"
#ipv4_bind_addresses = "127.0.0.1"
timestamp_type = 2
random_seed_file = "secret.txt"
hide_disclaimer = "YES"
chroot_dir = "C:/soft/maradns"
csv2 = {}
# our custom zone .some
csv2["some."] = "db.some"
csv2["example.com."] = "db.example.com"
```

**db.some**

```
*.% 172.16.1.1
```

Осталось только выбрать поднятый сервер в качестве основного днс в системе

```
ping www.some
```
