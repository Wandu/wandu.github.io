---
title: 정의하기
anchor: define-routes
---

## 정의하기 {#define-routes-title}

[Definition(앱 정의하기 항목) 파일](/#definition)의 `routes()`함수를 통해서 쉽게 정의할 수 있습니다.

기본적으로 RESTful 메서드 전체를 지원하고 있습니다.

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

그리고 `options`와 `patch`도 메서드로 제공하고 있습니다.

`get`, `post`, `put,` `delete`, `options`, `patch`는 3개의 매개변수를 요구합니다. 첫번째는 **URL 규칙**이고, 두번째는
**컨트롤러 클래스** 마지막 세번째는 **컨트롤러 클래스의 메서드**입니다. 3번째 매개변수는 생략가능하며 기본값은
`index`입니다.

**주의하세요!**

Wandu Router는 기본적으로 굉장히 Url에 예민한(Sensitive) 라우터입니다. 첫번째 매개변수에 `/users`를 앞에 슬래시(/)를
생략하면 동작하지 않을 수 있습니다. 이 예민한 특성을 이용해서 다음과 같은 Url 규칙도 생성할 수 있습니다.

```php
<?php

// ...생략...

public function routes(Router $router)
{
    $router->get('/users', UserController::class, 'index');

    $router->get('//users', UserController::class, 'indexOther1'); // 달라요!!
    $router->get('///users', UserController::class, 'indexOther2'); // 전혀 달라요!!
}

// ...생략...
```
