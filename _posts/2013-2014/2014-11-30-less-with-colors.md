---
layout: post
title: Делаем подсветку синтаксиса для less
type: post
categories:
- HowTo
tags:
- bash
- linux
- python
permalink: "/2014/11/30/%d0%b4%d0%b5%d0%bb%d0%b0%d0%b5%d0%bc-%d0%bf%d0%be%d0%b4%d1%81%d0%b2%d0%b5%d1%82%d0%ba%d1%83-%d1%81%d0%b8%d0%bd%d1%82%d0%b0%d0%ba%d1%81%d0%b8%d1%81%d0%b0-%d0%b4%d0%bb%d1%8f-less/"
---
![Подсветка синтаксиса в less]({{ site.baseurl }}/assets/images/2014/11/d0b2d18bd0b4d0b5d0bbd0b5d0bdd0b8d0b5_138.png){:.img-fluid}

Дефолтный вывод less  в консоли чрезвычайно скучный. И рассматривать километры одинаковых черно-белых листингов утомляет (или зеленых если у вас Ъ-хакерский терминал :)).

Так привнесем же цветность в наши черно-белые терминал.

```shell
 $ sudo yum install python-pygments
```

А затем прописываем в .bashrc

```
export LESS="-R"  
export LESSOPEN="|pygmentize -g -O encoding=utf8 %s"
```

Кодировку ставим на выбор. Но если чаще всего работаем в юникоде, то и оставляем юникод.

При этом будет подсвечиваться только вывод less, который вызван в дефолтной форме

```shell
 $ less filename
```

Вывод же less, который работает с перенаправленным вводом подсвечиваться на будет. :)

[Проект pygments](http://pygments.org/ "Python syntax highlighter").

