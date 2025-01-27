---
layout: post
title: 'Eclipse: корявый интерфейс в KDE'
type: post
categories:
- Разработка
tags:
- eclipse
- kde
- linux
permalink: "/2015/10/08/eclipse-%d0%ba%d0%be%d1%80%d1%8f%d0%b2%d1%8b%d0%b9-%d0%b8%d0%bd%d1%82%d0%b5%d1%80%d1%84%d0%b5%d0%b9%d1%81-%d0%b2-kde/"
---
![eclipse-theme-dialog]({{ site.baseurl }}/assets/images/2015/10/eclipse-theme-dialog.png){:.img-fluid}

Уже несколько вечеров я ломаю голову над вопросом: "почему в eclipse 4.5 (mars) застывает интерфейс при открытии выпадающих списков?"  
Проявляется так:

- открываем выпадающий список
- пытаемся выбрать в нем пункт, а пункт не выбирается

Закрыть такой "зависший список можно только прицельным щелчком на стрелочке или нажатием на esc.

Из других интерфейсных проблем: наползание виджетов друг на друга как на скрине в начале статьи.

И последняя проблема - это рандомные креши.

К слову, все это происходит в kde 4.

Я грешил на все: кривые rpm, неправильную сборку самой eclipse и всякое-разное.

Но оказалось все гораздо проще: креши и фризы связаны с темой oxygen-gtk (кто-бы мог подумать). И самый первый [репорт](https://bugs.kde.org/show_bug.cgi?id=339174) датируется аж 2014 годом. И его вроде даже закрыли.

В тему того же бага несколько другой: [Bug 470994 - [GTK3] Eclipse Mars on Linux with oxygen-gtk theme looks unfinished](https://bugs.eclipse.org/bugs/show_bug.cgi?id=470994).

Он как раз про вторую проблему с перекрывающимися виджетами.

Решается все просто: ставим нормальную тему gtk2 (bluecurve можно).  
И выбираем ее в настройках кедов.

![eclipse-theme-settings]({{ site.baseurl }}/assets/images/2015/10/eclipse-theme-settings.png){:.img-fluid}

Теперь с виджетами все ок, а выпадающие списки работают.  
![eclipse-theme-result]({{ site.baseurl }}/assets/images/2015/10/eclipse-theme-result.png){:.img-fluid}

