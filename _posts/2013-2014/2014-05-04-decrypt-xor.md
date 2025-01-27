---
layout: post
title: 'Шифр XOR: практика взлома.'
type: post
categories:
- Обработка текста
- JFF
tags:
- шифры
permalink: "/2014/05/04/%d1%88%d0%b8%d1%84%d1%80-xor-%d0%bf%d1%80%d0%b0%d0%ba%d1%82%d0%b8%d0%ba%d0%b0-%d0%b2%d0%b7%d0%bb%d0%be%d0%bc%d0%b0/"
---
В книге "Головоломки для хакера" в самой первой главе есть задача в которой читателю предлагается декодировать текст зашифрованный простым xor. При этом ключ неизвестен.

В качестве текста предлагается некий манифест "[The Conscience of a Hacker.txt](https://github.com/RussianPenguin/puzzles/blob/master/The%20Conscience%20of%20a%20Hacker.txt "The Conscience of a Hacker.txt")".

Приступим :)

Для начала нам нужно определить длину ключа.

- циклически смещаем текст на _i_ (от _1_ до _len(text)/2_) символов вправо
- для каждой итерации сравниваем количество совпадений символов оригинального (не смещенного) и нового текстов)
- смещение i при котором количество совпадений будет максимальным и есть искомая длина ключа

И кстати, почему _len(text)/2_? Правильнее конечно проверять все смещения вплоть до _len(text) - 1_, но эксперименты показали, что процент совпадений для сдвига _i_ и _n-i_ будет одинаков на больших текстах. Да и кто будет делать ключи длиною больше половины текста?

На практике это выглядит так:

- У нас есть шифротекст "abcdea"
- циклически сдвигаем его на _i = 1_ символов
- Получаем "aabcde"
- Ищем количество совпадений символов в строках "abcdea" и "aabcde"
- У нас есть одно совпадение в нулевом символе.
- Процент совпадений для сдвига _1_ равен ~16,7%
- Повторяем для всех сдвигов и выбираем тот, у которого процент наибольший

Круто. Длина ключа нам известна.

Теперь надо найти сам ключ.

Для этого поступим таким образом:

- Разбиваем текст на группы символов
- Количество групп равно длине ключа
```
Если у нас шифротекст "ciphertext" и ключ длиной 3 символа, то в первую группу попадут буквы c,h,t,t; во вторую: i, e, e; а в третью - p, r, x
```
- В каждую группу входят символы, которые кодируются _i-м_ символом ключа
- В каждой из групп ксорим символы с наиболее распространенным в алфавите символом (для английского языка это пробел)
- Считаем частоты каждого полученного символа среди группы
- Выбираем в качестве _i-й_ буквы ключа элемент с наибольшей частотой вхождения

Отлично. Потому что этот алгоритм успешно нашел ключ к шифротексту из задачи. Правда автор в решениях рекомендовал поксорить текст с фразой "The Conscience of a Hacker", а потом глазами искать что-то похожее на ключ...

[Исходники на гитхабе. Вместе с исходником загадки.](https://github.com/RussianPenguin/puzzles "Исходники к статье")

