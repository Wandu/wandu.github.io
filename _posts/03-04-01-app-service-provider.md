---
title: 앱 서비스프로바이더
anchor: app-service-provider
---

## 앱 서비스프로바이더 {#app-service-provider-title}

어플리케이션에서 사용할 내용을 주입하는 앱 서비스 프로바이더입니다.

```php
<?php
namespace Wandu\App;

use Wandu\DI\ContainerInterface;
use Wandu\DI\ServiceProviderInterface;
use Wandu\Foundation\Contracts\HttpErrorHandlerInterface;
use Wandu\Foundation\Contracts\KernelInterface;
use Wandu\Foundation\Error\DefaultHttpErrorHandler;

class ApplicationServiceProvider implements ServiceProviderInterface
{
    public function register(ContainerInterface $app)
    {
        $app->bind(HttpErrorHandlerInterface::class, DefaultHttpErrorHandler::class);
    }

    public function boot(ContainerInterface $app)
    {
        if ($app->has(KernelInterface::class)) {
            $kernel = $app->get(KernelInterface::class);
            $kernel['commands'] = require __DIR__ . '/../app/commands.php';
            $kernel['routes'] = require __DIR__ . '/../app/routes.php';
        }
    }
}
```
