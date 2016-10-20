---
title: 브릿지패키지
anchor: bridges
---

## 브릿지패키지 {#bridges_title}

Wandu는 모든 부분이 대체가 가능합니다. 만약에, 템플릿 엔진으로서 Twig를 사용하고 싶으면 Twig를 사용하면 됩니다. Latte를
사용하고 싶다면 Latte를 사용하면 됩니다. 데이터베이스 엔진도 마찬가지입니다. Eloquent를 사용하고 싶다면 Eloquent를,
Doctrin ORM을 사용하고 싶다면 그렇게 하시면 됩니다. 그리고 그 외에 패키지들도 마찬가지입니다. (물론, Adapter를 제작해야
할 때도 있습니다.)

기본으로 브릿지 소스를 제공하는 패키지들은 다음과 같습니다.

### 템플릿엔진

- PHP 자연어 (기본값)
- [Twig](http://twig.sensiolabs.org/)
- [Latte](https://latte.nette.org/)

### 데이터베이스

- [Eloquent](https://laravel.com/docs/master/eloquent)

### 로그

- [Monolog](https://github.com/Seldaek/monolog)
