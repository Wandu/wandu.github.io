---
title: Prefix 정의하기
anchor: define-routes-prefix
---

### Prefix 정의하기 {#define-routes-prefix-title}

반복되는 부분을 매번 정의할 때 번거로우면 하나로 합칠 수 있습니다.

```php
<?php

// ...생략...

public function routes(Router $router)
{
    $router->get('/users', UserController::class, 'index');
    $router->post('/users', UserController::class, 'store');
    
    $router->get('/users/{id:\d+}', UserController::class, 'show');
    $router->put('/users/{id:\d+}', UserController::class, 'update');
    $router->delete('/users/{id:\d+}', UserController::class, 'destroy');
}

// ...생략...
```

위 소스는 다음과 같이 교체할 수 있습니다.

```php
<?php

// ...생략...

public function routes(Router $router)
{
    $router->prefix('/users', function (Router $router) {
        $router->get('', UserController::class, 'index');
        $router->post('', UserController::class, 'store');
        
        $router->get('/{id:\d+}', UserController::class, 'show');
        $router->put('/{id:\d+}', UserController::class, 'update');
        $router->delete('/{id:\d+}', UserController::class, 'destroy');
    });
}

// ...생략...
```
