---
title: 컨트롤러(Controller)
anchor: define-controller
---

## 컨트롤러(Controller) {#define-controller-title}

컨트롤러는 별다른 제약사항이 없습니다. 일반 클래스를 사용하면 됩니다.

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

만약 위와 같이 `UserController`를 사용하기로 했다면, 컨트롤러는 다음과 같이 정의할 수 있습니다.

```php
<?php
use Psr\Http\Message\ServerRequestInterface;
use function Wandu\Http\response;

class UserController
{
    public function index(ServerRequestInterface $request)
    {
        return response()->create('index', 200); // PSR7 ResponseInterface
    }

    public function store(ServerRequestInterface $request)
    {
        return [
            'result' => true,
            'message' => 'store success!',
        ]; // json ok (if router use WanduResponsifier)
    }

    public function show(ServerRequestInterface $request)
    {
        $id = $request->getAttribute('id'); // {id:\d+}
        return 'show'; // string ok (if router use WanduResponsifier)
    }

    public function update(ServerRequestInterface $request)
    {
        $id = $request->getAttribute('id');
        foreach (range(0, 10) as $index) {
            yield $index;
        }
        // generator ok (if router use WanduResponsifier)
    }

    public function destroy(ServerRequestInterface $request)
    {
        $id = $request->getAttribute('id');
        return response()->create('destroy', 200);
    }
}
```

컨트롤러(Controller)에서 생성자와 모든 매서드는 Wandu의 컨테이너(Wandu DI)를 사용할 수 있도록 설정되어있습니다. 내부적으로 라우터는 Class Loader를 사용하는데, 이때 기본값이 `WanduLoader`로 되어있습니다. 이 Class Loader가 Wandu 컨테이너를 호출하는 역할을 합니다.

```php
<?php

class UserController
{
    public function __construct(SomethingOtherPackage $package)
    {
        $this->package = $package;
    }

    public function store(UserRepository $users, ServerRequestInterface $request)
    {
        // 생략
    }
}
```
