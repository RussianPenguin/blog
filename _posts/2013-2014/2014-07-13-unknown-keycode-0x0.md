---
layout: post
title: Unknown keycode 0x0
type: post
categories:
- HowTo
tags:
- java
- linux
permalink: "/2014/07/13/unknown-keycode-0x0/"
---
[Unknown keycode 0x0]({{ site.baseurl }}/assets/images/2014/07/d0b2d18bd0b4d0b5d0bbd0b5d0bdd0b8d0b5_013.png)

Вот такая веселая надпись будет нас ожидать в gnome если установлена раскладка отличная от en_*.

Это есть глобальная проблема большинства java-приложений в linux - в них не работаю горячие клавиши в раскладке отличной от латиницы (русской и любой другой).

От этого страдают все: и простые пользователи LibreOffice, и разработчики, которые вынождены пользоваться всякими разными ide, которые написаны на java.

Ага. А лечить-то как?

Для продуктов jetbrains существует два рецепта:

Добавить в idea.properties

```
-Dide.non.english.keyboard.layout.fix=true
```

Раньше работало, а теперь, увы, нет.

И второй способ, который можно использовать не только с jetbrains, но и с любым другим софтом на java.

Достаточно скачать [маленький jar](https://github.com/zheludkovm/LinuxJavaFixes "zheludkovm/LinuxJavaFixes") с гитхаба и следовать [инструкции](https://github.com/zheludkovm/LinuxJavaFixes/blob/master/README.md "zheludkovm/LinuxJavaFixes - readme"). Пока этот способ работает.

Проблема также замечена в smartsvn.

![cat phpstorm.vmoptions]({{ site.baseurl }}/assets/images/2014/07/d0b2d18bd0b4d0b5d0bbd0b5d0bdd0b8d0b5_014.png){:.img-fluid}

