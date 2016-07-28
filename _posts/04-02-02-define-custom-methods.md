---
title: 사용자 정의 메서드 구현하기
anchor: define-custom-methods
---

### 사용자 정의 메서드 구현하기 {#define-custom-methods-title}

만약에 [Varnish](https://varnish-cache.org)와 같은 도구를 사용하는데 `PURGE`라는 메서드의 라우트 룰을 정의하고 싶을
수 있습니다. 그럴때는 다음과 같이 정의할 수 있습니다.

```php
<?php

// ...생략...

public function routes(Router $router)
{
    $router->createRoute(['PURGE'], '/users', UserController::class, 'purge');
    $router->createRoute(['ETC', 'CUSTOM_METHOD'], '/users', UserController::class, 'something');
}

// ...생략...
```
