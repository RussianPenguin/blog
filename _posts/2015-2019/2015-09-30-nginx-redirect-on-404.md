---
layout: post
title: 'Nginx: редирект на другую платформу/площадку при 404'
type: post
categories:
- Разработка
tags:
- linux
- nginx
permalink: "/2015/09/30/nginx-%d1%80%d0%b5%d0%b4%d0%b8%d1%80%d0%b5%d0%ba%d1%82-%d0%bd%d0%b0-%d0%b4%d1%80%d1%83%d0%b3%d1%83%d1%8e-%d0%bf%d0%bb%d0%b0%d1%82%d1%84%d0%be%d1%80%d0%bc%d1%83%d0%bf%d0%bb%d0%be%d1%89%d0%b0%d0%b4/"
---
![nginx_with_two_upstream]({{ site.baseurl }}/assets/images/2015/09/d0b2d18bd0b4d0b5d0bbd0b5d0bdd0b8d0b5_027.png)Казалось бы: что может быть проще, чем взять два бекенда и одну точку входа.  
Когда первый бекенд не отвечает (или отвечает, но 404), то перенаправлять запрос (как можно прозначнее для пользователя) на второй бекенд.  
Правильно! :) Проще быть ничего не может.

```
location / {  
 # Отправляем все 404 на @backendB  
 error_page 404 = @backendB;  
 proxy_intercept_errors on;  
 log_not_found off;  
 # Try the proxy like normal  
 proxy_set_header X-Forwarded-For $remote_addr;  
 proxy_set_header Host $host;  
 proxy_set_header X-Real-IP $remote_addr;  
 proxy_pass http://127.0.0.1:8081;  
}

location @backendB {  
 # Резервный бекенд  
 proxy_set_header X-Forwarded-For $remote_addr;  
 proxy_set_header Host $host;  
 proxy_set_header X-Real-IP $remote_addr;  
 proxy_pass http://127.0.0.1:8082;

# Настоящую 404ую мы уже получим от этого бекенда.

}
```

