---
title: 둘러보기
anchor: tutorials
---

# 둘러보기 {#tutorials-title}

```
.
├ app
│  ├ commands.php
│  ├ config.php
│  ├ providers.php
│  └ routes.php
├ cache
├ migrations
├ public
│  ├ favicon.ico
│  ├ index.php
│  └ robots.txt
├ src
│  ├ Http
│  │  └ Controllers
│  │     └ HelloWorldController.php
│  ├ ApplicationDefinition.php
│  └ ApplicationServiceProvider.php
└ views
    └ welcome.php
```

- `app/commands.php` : 커맨드라인 명령어가 정의되어있습니다.
- `app/config.php` : 모든 설정이 정의되어있습니다.
- `app/providers.php` : 사용할 서비스 프로바디어가 정의되어있습니다.
- `app/routes.php` : 웹에서 사용할 라우팅 내용이 정의되어있습니다.

- `cache` : 캐시 디렉토리

- `migrations` : 데이터베이스 마이그레이션이 정의되어있습니다.

- `public/index.php` : 웹프로그램 시작점입니다.

- `src/Http/Controller/HelloWorldController.php` : 기본 헬로월드 컨트롤러입니다.
- `src/ApplicationDefinition.php` : 설정, 서비스프로바이더를 제공하는 파일입니다.
- `src/ApplicationServiceProvider.php` : 현재 앱의 서비스 프로바이더

- `views/welcome.php` : 기본 헬로월드 뷰 (템플릿 사용 안함)
