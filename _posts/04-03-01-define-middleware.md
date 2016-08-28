---
title: 미들웨어(Middleware)
anchor: define-middleware
---

## 미들웨어(Middleware) {#define-middleware-title}

미들웨어를 사용할 수 있습니다. 미들웨어 객체는 `Wandu\Router\Contracts\MiddlewareInterface`를 구현하면 됩니다. 다음은 세션을 사용하기 위한 `Sessionify` 미들웨어의 예시입니다.

```php
<?php
namespace Wandu\Router\Middleware;

use Closure;
use Psr\Http\Message\ServerRequestInterface;
use Wandu\Http\Cookie\CookieJarFactory;
use Wandu\Http\Exception\HttpException;
use Wandu\Http\Session\SessionFactory;
use Wandu\Router\Contracts\MiddlewareInterface;

class Sessionify implements MiddlewareInterface
{
    /** @var \Wandu\Http\Cookie\CookieJarFactory */
    protected $cookieJarFactory;

    /** @var \Wandu\Http\Session\SessionFactory */
    protected $sessionFactory;

    /**
     * @param \Wandu\Http\Cookie\CookieJarFactory $cookieJarFactory
     * @param \Wandu\Http\Session\SessionFactory $sessionFactory
     */
    public function __construct(
        CookieJarFactory $cookieJarFactory,
        SessionFactory $sessionFactory
    ) {
        $this->cookieJarFactory = $cookieJarFactory;
        $this->sessionFactory = $sessionFactory;
    }

    /**
     * {@inheritdoc}
     */
    public function __invoke(ServerRequestInterface $request, Closure $next)
    {
        $cookieJar = $this->cookieJarFactory->fromServerRequest($request);
        $session = $this->sessionFactory->fromCookieJar($cookieJar);

        $request = $request
            ->withAttribute('cookie', $cookieJar)
            ->withAttribute('session', $session);

        // run next
        try {
            $response = $next($request);
        } catch (HttpException $exception) {
            $response = $exception->getResponse();
            $this->sessionFactory->toCookieJar($session, $cookieJar);
            $exception->setResponse($this->cookieJarFactory->toResponse($cookieJar, $response));
            throw $exception;
        }

        $this->sessionFactory->toCookieJar($session, $cookieJar);
        return $this->cookieJarFactory->toResponse($cookieJar, $response);
    }
}
```

위 미들웨어(Middleware)는 다음과 같이 사용할 수 있습니다. 그룹으로 묶어서 사용할 수 있고, 단 하나의 라우터에 정의해서 사용할 수 있습니다.

```php
<?php
use Wandu\Router\Middleware\Sessionify;

// ...생략...

public function routes(Router $router)
{
	$router->middleware(Sessionify::class, function (Router $router) {
        // .. 다른 라우터 정의 ..
    });

    // or

    $router->get('/something', SomeController::class, 'thing', [
    	Sessionify::class,
    ]); // use in only one pattern
}

// ...생략...
```

### Prefix와 함께 사용하기

`group`이라는 매서드를 통해서 `middleware`와 `prefix`를 함께 사용할 수 있습니다.

```php
<?php
// ...생략...

public function routes(Router $router)
{
    $router->group([
        'middleware' => Sessionify::class,
        'prefix' => '/something'
    ], function (Router $router) {
    	// .. 다른 라우터 정의 ..
    });
}
```
