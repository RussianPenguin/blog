---
layout: post
title: 'Firefox: NS_ERROR_FILE_CORRUPTED'
type: post
categories:
- Разработка
- HowTo
tags:
- ошибки
- javascript
permalink: "/2015/04/15/firefox-ns_error_file_corrupted/"
---
Столкнулся сегодня с этой ошибкой в консоли фокса.

Причина проста: повреждение хранилища localStorage. Подробнее можно посмотреть официальную [документацию](https://developer.mozilla.org/en-US/docs/Web/Guide/API/DOM/Storage#Storage_location_and_clearing_the_data "Storage location and clearing the data").

Решается удалением файла ```
webappsstore.sqlite
```.

В коде же это исключение так же следует обрабатывать корректно (чтобы не пугать пользователя).

```javascript
try {  
 setLocalStorageItem(key, value);  
} catch(e) {  
 if(e.name == "NS_ERROR_FILE_CORRUPTED") {  
 showMessageSomehow("Sorry, it looks like your browser storage has been corrupted. Please clear your storage by going to Tools -> Clear Recent History -> Cookies and set time range to 'Everything'. This will remove the corrupted browser storage across all sites.");  
 }  
}
```

Странно, но в моем случае чистка всего-всего таки не помогла. Помогло ручное удаление файла.

