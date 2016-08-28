---
title: 별도 패키지로 사용하기
anchor: router-as-library
---

## 별도 패키지로 사용하기 {#router-as-library-title}

Wandu의 라우터는 별도의 패키지로 사용할 수 있도록 지원하고 있습니다.

```
composer install wandu/router:dev-develop
```

별도 패키지에서 라우터는 PSR7 기반의 패키지를 사용중이어야 합니다. 사용방법은 다음과 같습니다.

```php
<?php
use Wandu\Router\ClassLoader\DefaultLoader;
use Wandu\Router\Configuration;
use Wandu\Router\Dispatcher;
use Wandu\Router\Exception\HandlerNotFoundException;
use Wandu\Router\Exception\MethodNotAllowedException;
use Wandu\Router\Exception\RouteNotFoundException;
use Wandu\Router\Responsifier\NullResponsifier;
use Wandu\Router\Router;

// class loader는 라우터에서 정의한 클래스와 매서드를 불러옵니다.
// class loader will bring up the classes and methods defined in the router.
// 1. DefaultLoader
// 2. ArrayAccessLoader
// 3. WanduLoader : based on wandu/di package. (wandu default)
$classLoader = new DefaultLoader();

// responsifier는 일반 스칼라 변수를 Response 객체로 바꾸는 일을 합니다.
// responsifier turns scalar variable such as `string`, `int`, to the Response object.
// 1. NullResponsifier
// 2. WanduResponsifier : based on wandu/http package. (wandu default)
$responifier = new NullResponsifier();

$dispatcher = new Dispatcher(
    $classLoader,
    $responsifier,
    new Configure([
        'virtual_method_enabled' => true, // _method=PUT
        'cache_disabled' => false,
        'cache_file' => __DIR__ . '/routes.cache.php',
    ])
);

$dispatcher = $dispatcher->withRouter(function (Router $router) {
    $router->prefix('/admin', function (Router $router) {
        $router->get('/pages', PageController::class, 'index');
        $router->get('/users', UserController::class, 'index');
        $router->get('/users/{user}', UserController::class, 'show');
    });
});

try {
    $response = $dispatcher->dispatch($request); // $request is PSR7 ServerRequestInterface
} catch (MethodNotAllowedException $e) {
    // reutrn 405
} catch (RouteNotFoundException $e) {
    // return 404
} catch (HandlerNotFoundException $e) {
    // return 500
} catch (Exception $e) {
    // return 500    
} catch (Throwable $e) {
    // return 500
}
```


