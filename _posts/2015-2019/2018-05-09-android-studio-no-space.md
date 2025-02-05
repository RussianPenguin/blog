---
layout: post
title: 'Android Studio: на устройстве не осталось свободного места'
type: post
categories:
- Разработка
- HowTo
tags:
- android
- java
- linux
permalink: "/2018/05/09/android-studio-%d0%bd%d0%b0-%d1%83%d1%81%d1%82%d1%80%d0%be%d0%b9%d1%81%d1%82%d0%b2%d0%b5-%d0%bd%d0%b5-%d0%be%d1%81%d1%82%d0%b0%d0%bb%d0%be%d1%81%d1%8c-%d0%bc%d0%b5%d1%81%d1%82%d0%b0/"
excerpt: Заметка рассказывает о том, как избавиться от ошибки "на устройстве недостаточно
  свободного места" при обновлении AndroidStudio
---
![2018-05-09-13:48:05_911x394]({{ site.baseurl }}/assets/images/2018/05/2018-05-09-134805_911x394.png)Кто из вас не сталкивался с этим сообщением при обновлении студии под linux? Пожалуй каждый, кому доводилось с ней работать эту ошибку уже видел хотя бы раз и спешно шел в гугель для поиска ответа на вопрос что же ей не нравится и как это фиксить.

```
java.io.IOException: На устройстве не осталось свободного места
```

```
java.io.IOException: No space left on device
```

И так каждый раз. А вся проблема в том, что студия использует /tmp для распаковки установочных архивов перед копированием.

И чаще всего места там катастрофически недостает.

```shell
$ df -h |grep \/tmp  
tmpfs 3,9G 455M 3,4G 12% /tmp
```

Во многих дистрибутивах размер раздела выставляется равным половине объема озу. Решений несколько и каждый применяет то, которое ему нравится больше.

<!--more-->

## Временное увеличение размера /tmp

```shell
$ sudo mount -o remount,size=8G,noatime /tmp
```

Чем плохо: система активно начнем использовать подкачку и ей может просто не хватить суммарного объема свопа и ram.

## Переназначение временной директории для студии

![Выделение_002]({{ site.baseurl }}/assets/images/2018/05/d0b2d18bd0b4d0b5d0bbd0b5d0bdd0b8d0b5_002.png)

В открывшийся файл дописываем указание временной директории для виртуальной машины.

```
-Djava.io.tmpdir=/path-to-tmp-dir
```

Минус подхода: вся инфраструктура будет использовать эту временную директорию (компиляция, запуск и прочее), что приведет к замедлению работы если директория находится на диске (и неважно ssd это или hdd).

А еще все настройки будут теряться при обновлении студии (они хранятся в каталоге с настройками студии.

Есть другой вариант, позволяющий вынести настройки в глобальную область.

```shell
echo "-Djava.io.tmpdir=/tmp-dir-path" > ~/android-studio-tmp.fix  
STUDIO_VM_OPTIONS=~/android-studio.fix android-studio
```

## Указать временную директорию для всех приложений java

Прописать в .bashrc

```shell
export _JAVA_OPTIONS=-Djava.io.tmpdir=/path-to-tmp-dir
```

Это наименее разумный способ - он заставит абсолютно все java-приложения использовать указанную директорию.

## Указать отдельное размещение для распаковки временных файлов в момент установки

```shell
$ cd ~  
$ mkdir -p tmp/PackageOperation04  
$ cd /tmp  
$ ln -s $HOME/tmp/PackageOperation04
```

Самый разумный способ, имхо. Делать это нужно непосредственно перед тем, как вы нажмете кнопку "обновить".

## Литература

- [Configure Android Studio](https://developer.android.com/studio/intro/studio-config.html)
- [SO: /tmp directory in Linux Android SDK](https://stackoverflow.com/questions/38057884/tmp-directory-in-linux-android-sdk)
- [Android Studio – No space left on device](https://www.redips.net/linux/android-studio-no-space-left-on-device/)
- [Android Studio – No space left on device](https://bytefreaks.net/applications/android-studio-no-space-left-on-device)
