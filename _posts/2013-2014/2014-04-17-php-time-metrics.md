---
layout: post
title: 'PHP: метрика времени выполнения функции'
type: post
categories:
- Разработка
tags:
- php
permalink: "/2014/04/17/php-%d0%bc%d0%b5%d1%82%d1%80%d0%b8%d0%ba%d0%b0-%d0%b2%d1%80%d0%b5%d0%bc%d0%b5%d0%bd%d0%b8-%d0%b2%d1%8b%d0%bf%d0%be%d0%bb%d0%bd%d0%b5%d0%bd%d0%b8%d1%8f-%d1%84%d1%83%d0%bd%d0%ba%d1%86%d0%b8%d0%b8/"
---
Как нам узнать, сколько времени работает функция? Pinba, xhprof, xdebug?  
Да, но их нужно ставить на сервер и последние два модуля вносят немалый оверхед. Поэтому использовать их в продакшне нежелательно.  
Пинба классная, но нам нужно "вчера" и пока админ раскатывает модуль php нужно как-то извернуться.

Вспомним, что в php все неиспользуемые переменные будут удалены при выходе из функции. Ага!

```php
class Timer {  
 private $time = 0;  
 function __construct() {  
 $this->time = microtime(true);  
 }

function __destruct() {  
 $executionTime = microtime(true) - $this->time;  
 // делаем все, что нам надо: логгируем или еще чего  
 }  
}
```

Интегрируем в проект.

```php
function foo() {  
 $timer = new Timer();  
 // что-то делаем  
 sleep(5);  
 // на выходе будет вызван Time::__destructor().  
 // Так как все объекты уничтожаются  
}

foo();
```

