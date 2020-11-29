---
layout: post
title: 'PHP: array_map и ключи массива'
date: 2016-06-09 21:29:30.000000000 +03:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Разработка
- HowTo
tags:
- php
meta:
  _wpcom_is_markdown: '1'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '23675110719'
author:
  login: russianpenguin
  email: maksim.v.zubkov@gmail.com
  display_name: russianpenguin
  first_name: Maksim
  last_name: Zubkov
permalink: "/2016/06/09/php-array_map-%d0%b8-%d0%ba%d0%bb%d1%8e%d1%87%d0%b8-%d0%bc%d0%b0%d1%81%d1%81%d0%b8%d0%b2%d0%b0/"
---
Интересно, почему такой вопрос часто всплывает на форумах или где-то еще?

Сначала постараюсь ответить на вопрос "нафига?", а потом на вопрос "как?".

Мы привыкли, что в в питоне, js и множестве других языков и фреймворков есть функция map, которая применяет некоторую функцию-обработчик к каждому элементу в списке данных и возвращающую результат в том же порядке. [Пруф](https://en.wikipedia.org/wiki/Map_(higher-order_function)).

Но никто не ждал, что хеш-таблица и массив в контексте одного из языков будет означать одно и то же. Опять же [пруф](https://secure.php.net/manual/ru/language.types.array.php).

Этот постыдный момент из жизни структуры привел к тому, что одним из самых частых применений массивов (в пхп конечно же) стало создание отображений категория=\>какое-то значение.

А после того как у вас появилось отображение вы, вероятно, захотите это где-нибудь на сгенерированной странице отобразить.

Т.е. показать пользователю сам параметр, а скобках его категорию.

Как мы делаем это в языках (фреймворках), которые разделяют понятие массива и хеш-таблицы? Перебираем объект, который содержит категории и формируем по нему массив выводимых данных. Наверное так.

И тут мы открываем руководство по php и видим, что она применяет некий колбек ко всем элементам массива и после этого возвращает новый массив.

Удобно же! Применили колбек к массиву и получили обработанный.

[code lang="php"]$array = [  
 'category1' =\> 'first category',  
 'category2' =\> 'second category',  
];

var\_dump(array\_map(function(....[/code]

И что-то не заладилось. :) Ключи в колбек не попадают. Можно сделать все через foreach, но тогда нам потребуется еще одна переменная. А тут все было просто и наглядно.

И мы идем в гугель: "php array\_map with keys".

А зачем гуглить-то?

[code lang="php"]$array = [  
 'category1' =\> 'first category',  
 'category2' =\> 'second category',  
];

var\_dump(array\_map(function($key, $value) {  
 return "{$key} =\> {$value}";  
}, array\_keys($array), $array));[/code]

И все отлично работает. И притом правильно. Относительно конечно же. И никаких вам лишних переменных.

А тем временем в коде вновь и вновь появляются конструкции вида

[code lang="php"]$array = [  
 'category1' =\> 'first category',  
 'category2' =\> 'second category',  
];

$description = [];

foreach ($array as $key =\> $value) {  
 $description[] = "{$key} =\> {$value}";  
}[/code]

Или еще хуже.

[code lang="php"]$array = [  
 'category1' =\> 'first category',  
 'category2' =\> 'second category',  
];

$description = [];

array\_walk($array, function($value, $key) {  
 $description[] = "{$key} =\> {$value}";  
});[/code]

Проблема кейса из статьи может быть и раздута, но при вчитывании в сотни строк кода более-менее понятными сходу являются только первые два варианта.

Вариант же с array\_walk на понятность никак не претендует (как и решение подобной задачи при помощи .each в jquery).
