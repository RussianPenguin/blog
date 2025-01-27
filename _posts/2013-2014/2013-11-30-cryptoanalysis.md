---
layout: post
title: 'Практический криптоанализ: дилетант и афинные шифры'
type: post
categories:
- Обработка текста
tags:
- криптоанализ
permalink: "/2013/11/30/%d0%bf%d1%80%d0%b0%d0%ba%d1%82%d0%b8%d1%87%d0%b5%d1%81%d0%ba%d0%b8%d0%b9-%d0%ba%d1%80%d0%b8%d0%bf%d1%82%d0%be%d0%b0%d0%bd%d0%b0%d0%bb%d0%b8%d0%b7/"
---
Дело было вечером. Но делать было чего.

Возьмем некоторый текст и зашифруем его. А шифровать-то чем? Афинными шифрами конечно. Они-то самые простые. Залезем в <a href="http://en.wikipedia.org/wiki/Affine_cipher" title="Афинные шифры">педивикию</a> и посмотрим, что это за зверь. В множество афинных шифров входит <a href="http://en.wikipedia.org/wiki/Atbash" title="Атбаш">атбаш</a> и <a href="http://en.wikipedia.org/wiki/Caesar_cipher" title="Шифр Цезаря">шифр цезаря</a>.

Не будем сильно усложнять и возмем атбаш. Благо у него самый простой алфавит замены.

```
Исходный текст:
abcdefghijklmnopqrstuvwxyz

Зашифрованный текст:
ZYXWVUTSRQPONMLKJIHGFEDCBA
```
И константы a и b равны соотвестенно 25. Почему так - хорошо показано в <a href="http://en.wikipedia.org/wiki/Atbash#Relationship_to_the_affine_cipher" title="Связь шифтра атбаш и афинных шифров">вики</a>.

Но сейчас не об этом. Шифровать мы будем Стругацких: "Полдень XXII Век" на <a href="http://lib.ru/STRUGACKIE/engl_noon22.txt" title="Стругацкие: Полдень 22й век (eng)">английском</a>. Шифроваться ьудет только текст. Без html-разметки.

А после шифрования мы потеряем ключ и попробуем раскодировать.

<!--more-->

Поехали.

```
Cypher key: ZYXWVUTSRQPONMLKJIHGFEDCBA
```
Круто. Что-то получилось.

```
    Zipzwb Hgiftzghpb zmw Ylirh Hgiftzghpb. Mllm: 22mw xvmgfib



---------------------------------------------------------------
     © Zipzwb zmw Ylirh Hgiftzghpb
     © Gizmhozgvw uiln gsv Ifhhrzm yb Kzgirxp O. NxTfriv
     © Rmgilwfxgrlm yb Gsvlwliv Hgfitvlm
---------------------------------------------------------------


    Rmgilwfxgrlm



     Gsrh tivzg gzkvhgib lu rmgvidlevm hslig hglirvh zmw  mlevovggvh dzh hrc
bvzih rm gsv nzprmt. Rg rh ufoo lu zhglmrhsnvmgh,  zmw znlmt gsv tivzgvhg rh
gszg gsv hrc bvzih rmeloevw ziv 1960  gl 1966. Hkfgmrp,  zm lyqvxg zylfg gsv
hrav lu z yvzxs yzoo,  szw nzwv  rgh grmb yfg kilulfmw nzip lm sfnzm srhglib
ovhh gszm gdl zmw z szou bvzih yvuliv, wbrmt  rm  urugvvm dvvph.  (Droob Ovb
lmxv  ivnzipvw gszg  ru  gsv  vzigs dviv gsv  hrav  lu Hkfgmrp,  rg dlfow yv
hnllgsvi, zmw  ru gsv  liyrg  lu  Hkfgmrp  dviv ivkivhvmgvw yb  z driv,  blf
xlfowm&#039;g tvg z pmrggrmt mvvwov fmwvi rg.) Gsviv dviv uvd kvlkov lm vzigs dsl
fmwvihgllw gsv ufoo rnklig  lu gsrh zxsrvevnvmg rm zoo rgh kvinfgzgrlmh, yfg
znlmt gsvn xvigzrmob dviv gsv yilgsvih Ylirh zmw Zipzwb Hgiftzghpb.
```


Так. Ключ мы "забыли". Это плохо. Нам нужно это все расшифровать.

Что мы знаем? Только то, что текст зашифрован одной из разновидностей афинных шифров. Текст большой. Не предположительно, а точно написан на английском.<br />
Перебирать a и b, а потом проверять текст на наличие английских слов как-то не спортивно.

О! Попробуем частотный анализ. У нас есть классные <a href="http://en.wikipedia.org/wiki/Letter_frequency" title="Частотные характеристики латинского алфавита">таблицы распределений</a>.

Пишем парсер и натравливаем его на наш текст.

```
real freq
A => 0.08167
B => 0.01492
C => 0.02782
D => 0.04253
E => 0.12701
F => 0.02228
G => 0.00153
H => 0.06094
I => 0.06966
J => 0.02015
K => 0.00772
L => 0.04025
M => 0.02406
N => 0.06749
O => 0.07507
P => 0.01929
Q => 0.00095
R => 0.05987
S => 0.06327
T => 0.09056
U => 0.02758
V => 0.00978
W => 0.0236
X => 0.0015
Y => 0.01974
Z => 0.00074

crypt freq
A => 0.00128880980493
B => 0.0215979578601
C => 0.001652586766
D => 0.0216540834484
E => 0.0130647897161
F => 0.0281209984534
G => 0.0910544127185
H => 0.0625176691667
I => 0.0562461543578
J => 0.00104767764788
K => 0.0184777908968
L => 0.0775447757471
M => 0.0689367734854
N => 0.0220220178604
O => 0.0435825586616
P => 0.0128631533434
Q => 0.000974922255667
R => 0.0672488483861
S => 0.0587011291637
T => 0.0240550113914
U => 0.0184819483478
V => 0.121507741174
W => 0.0477316947433
X => 0.0234792044302
Y => 0.0143847804034
Z => 0.08176250977

key: ZYOIVBESLXPWNMRUCHJGFKDQTA
```

Ура. У нас есть ключ! Да вот только результат какой-то странный. :(

```
    Ahgady Sthcmatsgy aid Vohns Sthcmatsgy. Iooi: 22id weitchy



---------------------------------------------------------------
     © Ahgady aid Vohns Sthcmatsgy
     © Thaislated phof tre Hcssnai vy Bathnwg L. FwMcnhe
     © Nithodcwtnoi vy Treodohe Stchmeoi
---------------------------------------------------------------
Nithodcwtnoi Trns mheat tabesthy op nitehjokei sroht stohnes aid iokelettes jas snx yeahs ni tre fagnim. Nt ns pcll op astoinsrfeits, aid afoim tre mheatest ns trat tre snx yeahs nikolked ahe 1960 to 1966. Sbcting, ai ovuewt avoct tre snqe op a veawr vall, rad fade nts tniy vct bhopocid fahg oi rcfai rnstohy less trai tjo aid a ralp yeahs vepohe, dynim ni pnpteei jeegs. (Jnlly Ley oiwe hefahged trat np tre eahtr jehe tre snqe op Sbcting, nt jocld ve sfootreh, aid np tre ohvnt op Sbcting jehe hebheseited vy a jnhe, yoc wocldi't met a ginttnim ieedle cideh nt.) Trehe jehe pej beoble oi eahtr jro cidehstood tre pcll nfboht op trns awrnekefeit ni all nts behfctatnois, vct afoim tref wehtanily jehe tre vhotrehs Vohns aid Ahgady Sthcmatsgy.
```

Это белиберда какая-то, а не текст. Чешем то, чем думаем. Нет, отпределенно прослеживается какая-то логика и даже какие-то слова заметны, но этот результат нас не устраивает. :)

А что если...

А что если у нас есть какая-то буква для которой нам точно будет известно ее отражение в криптованном тексте?

Пусть нам известен такой символ. X - его реальный код, а y - код в криптованном файле.

Тогда нам точтаточно подобрать такие a и b, что равество E(x) == y будет истиной.

```text
E(x) = (ax+b) mod 26
```

Что нам известно про a и b? Сидели ли они на трубе? Возможно.

```text
1<=a<26 b - любое, но при b < 0 или b > 25 его проверка бессмысленна. Полькольку эти интервалы отражаются на 0..25 a и 26 взаимно простые
```

Уже хорошо. Но постойте. А вдруг надутся несколько пар значений, которые дадут E(x) == y?

Да. Такое может быть. И для этого мы вспомним, о том, что уже нашли некоторый ключ ранее (частотным анализом).

Что мы можем сделать? Мы можем посчитать новые ключи при выбранных a и b и сравнить, насколько сильно различается ключ сгенерированный и ключ найденный.

Вроде бы логично. Опираться в своих поисках мы будем на букву с самой большой частотой появления - это V (freq:0.121507741174) в криптотексте. В реальности - это очевидно будет буква E (freq: 0.12701).

Считаем.

```
A and B was found 1 17 
key and key quality: RSTUVWXYZABCDEFGHIJKLMNOPQ 0.0769230769231 
A and B was found 3 9 
key and key quality: JMPSVYBEHKNQTWZCFILORUXADG 0.0384615384615 
A and B was found 5 1 
key and key quality: BGLQVAFKPUZEJOTYDINSXCHMRW 0.0384615384615 
A and B was found 7 19 
key and key quality: TAHOVCJQXELSZGNUBIPWDKRYFM 0.115384615385 
A and B was found 9 11 
key and key quality: LUDMVENWFOXGPYHQZIRAJSBKTC 0.0769230769231 
A and B was found 11 3 
key and key quality: DOZKVGRCNYJUFQBMXITEPALWHS 0.0384615384615 
A and B was found 15 13 
key and key quality: NCRGVKZODSHWLAPETIXMBQFUJY 0.0769230769231 
A and B was found 17 5 
key and key quality: FWNEVMDULCTKBSJARIZQHYPGXO 0.0769230769231 
A and B was found 19 23 
key and key quality: XQJCVOHATMFYRKDWPIBUNGZSLE 0.0384615384615 
A and B was found 21 15 
key and key quality: PKFAVQLGBWRMHCXSNIDYTOJEZU 0.0384615384615 
A and B was found 23 7 
key and key quality: HEBYVSPMJGDAXUROLIFCZWTQNK 0.115384615385 
A and B was found 25 25 
key and key quality: ZYXWVUTSRQPONMLKJIHGFEDCBA 0.423076923077 
ZYOIVBESLXPWNMRUCHJGFKDQTA
```

Ого! У нас есть ключ, которому мы можем доверять на 42% (главное, что не 142. ага). Здорово. И a=b=25. После применения ключа на текст мы получаем осмысленный результат.

Есть ли плюсы? Да. Возможно. Из-за объемов текста перебирать все 312 ключей неинтересно. А совмещение с частотным анализом дает сокращение вариантов до 11 значений, которые ранжированы по степени доверия.

