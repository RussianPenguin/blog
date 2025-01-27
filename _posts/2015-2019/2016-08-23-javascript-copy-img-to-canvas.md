---
layout: post
title: 'JavaScript: копируем img в canvas'
type: post
categories:
- Обработка изображений
- Разработка
tags:
- javascript
permalink: "/2016/08/23/javascript-%d0%ba%d0%be%d0%bf%d0%b8%d1%80%d1%83%d0%b5%d0%bc-img-%d0%b2-canvas/"
---
Существует несколько способов скопировать имеющееся на странице изображение (img) в canvas. Конечно все они сводятся к простейшему drawImage, но каждый из этих методов применим в своем контексте.

Первый и самый очевидный (а еще универсальный) - загрузить изображение при помощи объекта Image. Он работает только в случае, когда у img есть атрибут src.

```javascript
var img = new Image();  
var srcImg = document.getElementById('src-img');  
img.onload = function() {  
 // создаем (или получаем) канву  
 var backCanvas = document.getElementById('dest-canvas');  
 backCanvas.width = this.width;  
 backCanvas.height = this.height;  
 var backCtx = backCanvas.getContext('2d');

// рисуем  
 backCtx.drawImage(this, 0,0);  
};  
img.setAttribute('src', srcImg.src);
```

Недостаток - еще один запрос в сеть.

Второй способ - менее универсален. Картинка у нас уже есть - почему ее нельзя нарисовать на канве?

```javascript
var backCanvas = document.getElementById('dest-canvas');  
var srcImg = document.getElementById('src-img');  
backCanvas.width = srcImg.naturalWidth;  
backCanvas.height = srcImg.naturalHeight;

var backCtx = backCanvas.getContext('2d');

// рисуем картинку  
backCtx.drawImage(srcImg, 0,0);
```

И вот тут мы используем naturalWidth/naturalHeight. Зачем?

Все очень просто. Если в первом случае width и height означали размеры картинки, то во втором - это будут физические размеры элемента img на странице. И если картинка внутри него будет отмасштабирована, то мы мы скопируем только часть картинки размером width на height.

Как на иллюстрации ниже (картинка там имеет разрешение в десять раз больше нежели физические размеры img и масштабируется).

![Выделение_001]({{ site.baseurl }}/assets/images/2016/08/d0b2d18bd0b4d0b5d0bbd0b5d0bdd0b8d0b5_001.png){:.img-fluid}

А свойства naturalWidth/naturalHeight не поддерживаются в ряде браузеров. поэтому способ универсальным назвать нельзя.

