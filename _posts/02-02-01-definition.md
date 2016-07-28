---
title: 앱 정의하기
anchor: definition
---

## 앱 정의하기 {#definition-title}

설치 후, 생성된 `.wandu.php`파일을 열어보면 `Wandu\Foundation\Contracts\DefinitionInterface` 인터페이스를 상속받은
클래스 객체를 반환하는 것을 볼 수 있습니다.

```php
<?php
use YourNamespace\ApplicationDefinition;

return new ApplicationDefinition(require __DIR__ . '/.wandu.config.php');
```

해당 클래스를 열어보면 크게 4개로 구분된 것을 볼 수 있습니다. (물론 기본 클래스인 `StandardDefinition`을 상속받아서
생략된 메서드가 존재할 수도 있습니다.) 이를 통해 Wandu Framework는 어플리케이션 전체를 정의할 수 있습니다.

 - `configs()`
 - `providers()`
 - `routes()`
 - `commands()`

각각이 하는 일은 단순합니다.

**Config**

`configs()`는 앱의 설정을 읽어들입니다. 이 설정은 배열의 형태를 유지하고 있습니다. 그래서 내부에 있는 각종 클래스에서
해당 값을 사용할 수 있습니다. <!-- @todo 자세한 내용은 Config 항목을 참고하세요. -->

**Provider**

`providers()`는 앱의 서비스프로바이더를 읽어들입니다. 만약에 이후에 다른 라이브러리를 설치한 경우 해당 영역에
서비스프로바이더를 추가하여야 합니다. <!-- @todo 자세한 내용은 Container/ServiceProvider 항목을 참고하세요. -->

**Routes**

`routes()`는 웹에서 사용할 URL 라우팅 규칙을 읽어들입니다. 자세한 내용은 [Router](/#router)항목을 참고하세요.

**Commands**

`commands()`는 콘솔에서 사용할 커맨드를 읽어들입니다. <!-- @todo 자세한 내용은 Command 항목을 참고하세요. -->

정리하자면, `.wandu.php`파일을 통해서 **공통으로 사용되는 값(Config)**, **공통으로 사용되는 클래스 및 함수(Providers)**,
**웹에서 사용할 규칙(Routes)** 그리고 **콘솔에서 사용할 규칙(Commands)**을 정의합니다.
