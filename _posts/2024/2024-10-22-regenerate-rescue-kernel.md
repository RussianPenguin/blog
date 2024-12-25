---
layout: post
title: 'Linux: Создание rescue-записи grub в Fedora'
type: post
status: publish
categories:
- HowTo
tags:
- linux
permalink: "/2024/10/22/regenerate-rescue-kernel"
---

Для систем, которые существуют очень долго и обновляются через версии есть опасность того, что rescue ядро не заведётся. Вернее оно заведётся, но работать будет некомфортно.

А так как механизма обновления ядра в дефолте нет, то стоит производить его руками хотя бы раз в несколько версий дистрибутива.

```shell
$ sudo rm /boot/*rescue*
$ sudo /usr/lib/kernel/install.d/51-dracut-rescue.install add "$(uname -r)" /boot "/boot/vmlinuz-$(uname -r)"
```