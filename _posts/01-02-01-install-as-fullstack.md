---
title: 풀스택 프레임워크로 사용하기
anchor: install-as-fullstack
---

## 풀스택 프레임워크로 사용하기 {#installation}

[Composer](https://getcomposer.org)를 통해서 설치할 수 있습니다. 아직은 개발버전이라서 뒤에 "dev-develop"을 만드시 함께
적어야 합니다.

```sh
cd /your/project/path
composer require "wandu/framework:dev-develop"
```

설치후에 `vendor/bin` 디렉토리에 있는 `wandu`를 통해 `install`명령어를 실행할 수 있습니다.

```
vendor/bin/wandu install
```

그러면 두개의 질문을 물어봅니다. 첫번째는 설치경로, 두번째는 사용할 어플리케이션의 네임스페이스입니다.
첫번째 질문은 특수한 상황이 아니라면 그냥 기본값을 사용하면 됩니다.

```
 install path? [/your/project/path]:
 >
```

두번째 질문은 어플리케이션에서 사용할 네임스페이스입니다. 원하는 값을 입력하세요. 그리고 이왕이면 PSR-4 규칙에 따라서,
PascalCase로 작성해주시기 바랍니다.

```
 app namespace? [Wandu\App]:
 >
```

모든 설치가 끝났습니다.

`public/index.php` 파일이 보이시나요? 이제 이 파일을 통해서 어플리케이션을 실행할 수 있습니다.

```sh
php -S 127.0.0.1:8080 -t public
```

위 명령어를 통해서 어플리케이션을 실행해보세요. 그리고 브라우저에 `127.0.0.1:8080`을 입력해보세요.
정상적으로 설치되었다면 **Hello Wandu**라는 메시지가 출력됩니다.
