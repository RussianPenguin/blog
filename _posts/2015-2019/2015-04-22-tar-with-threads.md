---
layout: post
title: Многопоточная компрессия и tar
type: post
categories:
- HowTo
tags:
- bash
- linux
permalink: "/2015/04/22/%d0%bc%d0%bd%d0%be%d0%b3%d0%be%d0%bf%d0%be%d1%82%d0%be%d1%87%d0%bd%d0%b0%d1%8f-%d0%ba%d0%be%d0%bc%d0%bf%d1%80%d0%b5%d1%81%d1%81%d0%b8%d1%8f-%d0%b8-tar/"
---
Сколько можно? :) Ядер все больше и больше, а

```shell
$ tar -cjf /mnt/_backup/`date '+%Y-%m-%d_%H-%M-%S'`.tbz2 /home
```

как работал в один поток, так и работает.

Есть два многопоточных решения:

- pbzip2 - параллельный bzip
- pigz - параллельный gzip

```shell
$ tar -c /home | pbzip2 -vc -9 -p7 /mnt/_backup/`date '+%Y-%m-%d_%H-%M-%S'`.tbz2
```

Опцией -p# можно управлять количеством ядер, которые будет использовать архиватор. Нормально - это N-1.

Аналогично и для gzip

```shell
$ tar -c /home | pigz -vc -9 -p7 /mnt/_backup/`date '+%Y-%m-%d_%H-%M-%S'`.tbz2
```

С удивлением открыл для себя, что - в bash по дефолту означает stdout. И

```shell
$ tar -cf - file
```

будет использовать в качестве файла stdout. :)

