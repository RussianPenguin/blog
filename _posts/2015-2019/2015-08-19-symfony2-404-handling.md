---
layout: post
title: 'Symfony2: обработка исключения 404'
type: post
categories:
- HowTo
tags:
- php
- symfony2
permalink: "/2015/08/19/symfony2-%d0%be%d0%b1%d1%80%d0%b0%d0%b1%d0%be%d1%82%d0%ba%d0%b0-%d0%b8%d1%81%d0%ba%d0%bb%d1%8e%d1%87%d0%b5%d0%bd%d0%b8%d1%8f-404/"
---
Допустим, что нам нужно обработать исключение 404 и вместо стандартного ответа сервера (или шаблона ошибки ответить специфической страницей.

Как кастомизировать страницы подробно описано в [официальной документации](http://symfony.com/doc/current/cookbook/controller/error_pages.html).

Нас будет интересовать пункт работы с эвентом kernel.exception.

Можно посмотреть один из [примеров](http://symfonybricks.com/en/brick/custom-exception-page-404-not-found-and-other-exceptions) его использования.

Наш кейс: если symfony не смог обработать роут, то управление передается своему обработчику.

```php
namespace AppBundle\Listener;  
use Symfony\Component\HttpKernel\Event\GetResponseForExceptionEvent;  
use Symfony\Component\HttpFoundation\Response;  
use Symfony\Component\HttpKernel\Exception\HttpExceptionInterface;  
use Symfony\Bundle\FrameworkBundle\Templating\EngineInterface;  
use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

class ExceptionListener  
{  
 protected $templating;  
 protected $kernel;

public function __construct(EngineInterface $templating, $kernel)  
 {  
 $this->templating = $templating;  
 $this->kernel = $kernel;  
 }

public function onKernelException(GetResponseForExceptionEvent $event)  
 {  
 if ($event->getException() instanceof NotFoundHttpException) {  
 $response = new Response();  
 $response->setContent("Какие-то данные");  
 //$response->setStatusCode(200) работать не будет  
 $response->headers->set('X-Status-Code', 200);  
 $event->setResponse($response);  
 }  
 }  
}
```

В config.yml прописывем что-то вроде

```
services:  
 kernel.listener.app_exception_listener:  
 class: AppBundle\Listener\ExceptionListener  
 arguments: [@templating, @kernel]  
 tags:  
 - { name: kernel.event_listener, event: kernel.exception, method: onKernelException }
```

И обратите внимание, что переопределение статуса ответа в виде  
```php
$response->setStatusCode(200)
```

работать не будет. Мы не сможем переопределить статус отвта на 2хх с помощью этого метода (на любой другой можно).

Для того, чтобы мы могли переопределить ответ 404 нужно использовать метод

```php
$response->headers->set('X-Status-Code', 200);
```

