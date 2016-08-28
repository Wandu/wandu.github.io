---
title: 마이크로 프레임워크로 사용하기
anchor: install-as-micro
---

## 마이크로 프레임워크로 사용하기 {#install-as-micro-title}

Container는 프레임워크를 구성하는 가장 작은 단위입니다. 컨테이너와 Http 라이브러리, 라우터만 있으면 가장 작은 프레임워크를 구현할 수 있습니다. 

Wandu에서 제공하는 3개의 패키지를 설치합니다. 참고로, 프레임워크 전체(`wandu/framework`)를 설치하셨다면 다음 내용은 생략해도 좋습니다.

```sh
cd /your/project/path
composer require wandu/di wandu/http wandu/router
```

```php
<?php
use Wandu\Config\Config;
use Wandu\DI\Container;
use Wandu\Http\HttpServiceProvider;
use Wandu\Http\Psr\Factory\ServerRequestFactory;
use Wandu\Http\Psr\Sender\ResponseSender;
use Wandu\Router\Dispatcher;
use Wandu\Router\Router;
use Wandu\Router\RouterServiceProvider;

require __DIR__ . '/vendor/autoload.php';

class HelloController
{
    public function index()
    {
        return 'hello string';
    }
}

$container = new Container();

$container->instance('config', new Config([]));

$container->register(new HttpServiceProvider());
$container->register(new RouterServiceProvider());

$container->boot();

$dispatcher = $container->get(Dispatcher::class);

$request = $container->get(ServerRequestFactory::class)->createFromGlobals();
$response = $dispatcher->withRoutes(function (Router $router) {
    $router->get('/', HelloWorldController::class);
})->dispatch($request);

$container->get(ResponseSender::class)->sendToGlobal($response);
```
