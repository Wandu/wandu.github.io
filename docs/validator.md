---
layout: default
title: Validator
anchor: validator
---

# Validator {#validator-title}

올바른 값을 확인하기 위한 벨리데이터입니다.

## 사용법

헬퍼(`validator()`)를 통해 바로 사용할 수 있습니다. 각각의 벨리데이터는 파이프(`|`)를 통해 연결할 수 있습니다.

```php
<?php
use function Wandu\Validator\validator;

$validator = validator()->from([
    'username' => 'required|reg_exp:/^[a-zA-Z0-9_-]{4,30}$/',
    'password' => 'required|length_min:8|length_max:32',
    'email' => 'required|email',
    'company' => [
        'name' => 'required|string',
        'description' => 'string',
    ],
    'friends' => validator()->collection([
        'username' => 'reg_exp:/^[a-zA-Z0-9_-]{4,30}$/',
    ]),
]);

$validator->assert([
    'username' => 'wan2land',
    'password' => 'password',
    'email' => 'wan2land@gmail.com',
    'company' => [
        'name' => 'wandu',
    ],
    'friends' => [
        [
            'username' => 'wan3land',
        ],
        [
            'username' => 'wan4land',
        ],
    ]
]);
```

## 지원하는 메서드

- `validate($data)`
- `assert($data)`

### validate

`validate`메서드는 검증 결과를 `true`/`false`로 반환해줍니다. 그리고 잘못된 값이 있으면 그 즉시 테스트를 종료합니다.
단순히 테스트 성고유무만 알고싶다면 해당 메서드를 사용하면 됩니다.

```php
<?php
if ($validator->validate(/* some value */)) {
    // success
} else {
    // fail
}

```

### assert

`assert`메서드는 검증 결과를 `Exception`을 통해서 알려줍니다. 또한 테스트 중 잘못된 값이 있더라도 종료하지 않고 끝까지
모든 로직을 다 수행합니다. 폼 체크 같은 것을 할때 굉장히 유용합니다.

```php
<?php

try {
    $validator->assert(/* some value */);
} catch (Wandu\Validator\Exception\InvalidValueException $exception) {
    // catch
}

```

그리고 에러 정보는 `Wandu\Validator\Exception\InvalidValueException` 객체를 통해서 반환됩니다. 에러 정보를 보고 싶다면
`getTypes`를 통해 쉽게 확인할 수 있습니다.

```php
<?php

try {
    $validator->assert(/* some value */);
} catch (Wandu\Validator\Exception\InvalidValueException $exception) {
    $exception->getTypes(); // ['length_min:4@username', 'required@password' ... ]
}

```

여기서 반환되는 에러는 다음의 규칙을 따라서 반환됩니다.

`에러이름:매개변수1,매개변수2@에러위치1.에러위치2`

예를들어서 다음과 같은 벨리데이터가 있다고 가정해봅시다. `collection`에서 두번째 값을 잘못 넣었습니다.

```php
<?php

$validator = validator()->from([
    'user' => [
        'articles' => validator()->collection([
            'title' => 'required|length_max:20',
        ]),
    ],
]);
try {
    $validator->assert([
        'user' => [
            'articles' => [
                [
                    'title' => 'safe title',
                ],
                [
                    'title' => 'none safe title.............................',
                ],
            ]
        ]
    ]);
} catch (InvalidValueException $e) {
    print_r($e->getTypes()); // error "length_max:20@user.articles.1.title"
}

```

그러면 `length_max:20@user.articles.1.title` 에러가 출력됩니다.

## 벨리데이터 호출

벨리데이터는 3가지 방식에 의해 불러올 수 있습니다.

1. `validator()`
1. `validator()->from( ... )`
1. 직접 객체 선언.

### 메서드 통해 호출하기 (`validator()`)

헬퍼에는 기본 벨리데이터를 사용할 수 있도록 메서드를 지원합니다.

```php
<?php
$validator = validator()->integer();
$validator = validator()->lengthMin(5);
$validator = validator()->regExp('/^\d{1,8}-[a-zA-Z0-9]+$/');
```

위와 같이 사용할 수 있습니다. 메서드이기 때문에 camelCase를 사용합니다.

### 빌더를 통해 호출하기 (`validator()->from()`)

헬퍼에 `from`메서드에는 스트링을 통해서 호출할 수 있도록 지원하고 있습니다.

```php
<?php
$validator = validator()->from('integer');
$validator = validator()->from('length_min:5');
$validator = validator()->from('reg_exp:/^\d{1,8}-[a-zA-Z0-9]+$/');
```

위와 같이 사용할 수 있습니다. 스트링이기 때문에 underbar_case를 사용합니다. 매개변수는 `:` 뒤에 정의할 수 있습니다.

### 직접 선언

객체를 직접 선언해서 사용할 수 있습니다.

```php
<?php
$validator = new \Wandu\Validator\Rules\IntegerValidator();
$validator = new \Wandu\Validator\Rules\LengthMinValidator(5);
$validator = new \Wandu\Validator\Rules\RegExpValidator('/^\d{1,8}-[a-zA-Z0-9]+$/');
```

모든 객체는 PascalCase를 따르고 있으며 항상 뒤(suffix)에 `Validator`라는 이름이 붙습니다.

## 부정형 벨리데이터

주어진 타입과 반대인 값을 체크할 때 사용할 수 있습니다. 사용법은 단순합니다. `!` 하나만 해당 벨리데이터 앞에 붙여주면
됩니다.

```php
<?php

$validator = validator()->from([
    'user' => '!integer',
]);

```

혹은, 다음과 같이 직접 선언도 가능합니다.

```php
<?php
use Wandu\Validator\Rules\IntegerValidator;
use Wandu\Validator\Rules\NotValidator;

$validator = validator()->from([
    'user' => new NotValidator(new IntegerValidator()),
]);

```

## 정의된 벨리데이터

 - `required()`
 - `not(\Wandu\Validator\Contracts\ValidatorInterface $validator)`
 - `array(array $attributes = [])`
 - `collection($rule = null)`
 - `iterable($rule = null)`
 - `arrayable(array $attributes = [])`
 - `object(array $properties = [])`
 - `integer()`
 - `boolean()`
 - `float()`
 - `string()`
 - `integerable()`
 - `floatable()`
 - `numeric()`
 - `stringable()`
 - `printable()`
 - `min(int $min)`
 - `max(int $max)`
 - `lengthMin(int $min)`
 - `lengthMax(int $max)`
 - `email(\Egulias\EmailValidator\Validation\EmailValidation $validation = null)`
 - `regExp(string $pattern)`

### required

다른 벨리데이터의 경우 `null`이나 빈 스트링(`''`)을 정상처리합니다. 이 두가지 값에대해서 에러를 처리하고 싶다면 해당
벨리데이터를 사용하면 됩니다.

```php
<?php
validator()->required()->validate(null); // false
validator()->required()->validate(''); // false
validator()->required()->validate('something'); // true
```

### not

항상 안에 있는 결과의 부정형을 반환합니다.

```php
<?php
validator()->not(validator()->required())->validate(null); // true
validator()->not(validator()->required())->validate(''); // true
validator()->not(validator()->required())->validate('something'); // false
```

### array

배열(`array`) 내에 있는 키(key)값을 확인하고 그 각각에 대한 벨리데이터를 다시 처리합니다.

```php
<?php
$validator = validator()->array([
	'name' => validator()->string(),
	'username' => 'string|length_min:2',
]);
$validator->validate([
	'name' => 'something',
	'username' => 'foo...',
]); // true
$validator->validate([
	'name' => 'something',
	'username' => '.',
]); // false
```

### collection

`Traversable`객체 혹은 배열(`array`)을 체크합니다.

```php
<?php
$validator = validator()->collection([
	'name' => validator()->string(),
	'username' => 'string|length_min:2',
]);
$validator->validate([]); // true
$validator->validate(
[
	[
		'name' => 'something',
		'username' => 'foo...',
	],
	[
		'name' => 'something',
		'username' => 'foo...',
	],
]); // true
$validator->validate([
	'name' => 'something',
	'username' => 'foo...',
]); // false (this is not traversable)
```

### iterable

`collection`과 동일합니다.

### arrayable

`array`와 유사합니다. 배열(`array`)말고도 `ArrayAccess`객체도 체크할 수 있습니다.

```php
<?php
$validator = validator()->arrayable([
	'name' => validator()->string(),
	'username' => 'string|length_min:2',
]);
$validator->validate([
	'name' => 'something',
	'username' => 'foo...',
]); // true
$validator->validate(new ArrayObject([
	'name' => 'something',
	'username' => 'foo...',
])); // true
```

### object

`array`와 유사합니다. 그냥 객체(`object`) 차제를 체크합니다.

```php
<?php
$validator = validator()->object([
	'name' => validator()->string(),
	'username' => 'string|length_min:2',
]);
$validator->validate([
	'name' => 'something',
	'username' => 'foo...',
]); // false

$object = new stdClass();
$object->name = 'something';
$object->username = 'foo...';

$validator->validate($object); // true
```

### integer

정확한 정수형 데이터를 체크합니다.

```php
<?php
validator()->integer()->validate(10); // true
validator()->integer()->validate('10')); // false
validator()->integer()->validate(10.0); // false
```

### boolean

정확한 불리언형 데이터를 체크합니다.

```php
<?php
validator()->boolean()->validate(true); // true
validator()->boolean()->validate(false)); // true
validator()->boolean()->validate(1); // false
validator()->boolean()->validate(0); // false
```

### float

정확한 실수형 데이터를 체크합니다.

```php
<?php
validator()->float()->validate(1.0); // true
validator()->float()->validate(-1.1)); // true
validator()->float()->validate(1); // false
validator()->float()->validate('1.1'); // false
```

### string

```php
<?php
validator()->string()->validate('string!!!'); // true
validator()->string()->validate(1); // false
```

### integerable

정수로 변형할 수 있는 모든 형태를 체크합니다.

```php
<?php
validator()->integerable()->validate('10'); // true
validator()->integerable()->validate(10); // true
```

### floatable

실수로 변형할 수 있는 모든 형태를 체크합니다. 사실상 `is_numeric`과 동일합니다.

```php
<?php
validator()->floatable()->validate('1.1'); // true
validator()->floatable()->validate(1.1); // true
validator()->floatable()->validate('1'); // true
validator()->floatable()->validate(1); // true
```

### numeric

`floatable`과 동일합니다.

### stringable

문자열로 바꿀 수 있는 모든 값을 체크합니다. 사실상 `is_scalar`과 동일합니다.

```php
<?php
validator()->stringable()->validate('1.1'); // true
validator()->stringable()->validate(1.1); // true
validator()->stringable()->validate('1'); // true
validator()->stringable()->validate(1); // true
validator()->stringable()->validate(true); // true
validator()->stringable()->validate(false); // true
validator()->stringable()->validate('string~~'); // true
validator()->stringable()->validate([]); // false
```

### printable

`stringable`과 유사한데, 여기서는 `echo`함수를 통해 찍을 수 있는 모든 내용을 포함합니다.

```php
<?php
validator()->printable()->validate('1.1'); // true
validator()->printable()->validate(1.1); // true
validator()->printable()->validate('1'); // true
validator()->printable()->validate(1); // true
validator()->printable()->validate(true); // true
validator()->printable()->validate(false); // true
validator()->printable()->validate('string~~'); // true

class Something
{
    public __toString()
    {
        return "something class!";
    }
}

validator()->printable()->validate(new Something()); // true
```

### min

최소값을 체크합니다. 대소를 비교할 수 있는 객체도 넣을 수 있습니다.

```php
<?php
validator()->min(5)->validate(100); // true
validator()->min(5)->validate(6); // true
validator()->min(5)->validate(5); // true
validator()->min(5)->validate(4); // false

// Carbon is datetime Library
validator()->min(new Carbon('-1 year'))->validate(new Carbon); // true
validator()->min(new Carbon('+1 year'))->validate(new Carbon); // false
```

### max

최대값을 체크합니다. 대소를 비교할 수 있는 객체도 넣을 수 있습니다.

```php
<?php
validator()->max(5)->validate(0); // true
validator()->max(5)->validate(4); // true
validator()->max(5)->validate(5); // true
validator()->max(5)->validate(6); // false

// Carbon is datetime Library
validator()->max(new Carbon('-1 year'))->validate(new Carbon); // false
validator()->max(new Carbon('+1 year'))->validate(new Carbon); // true
```

### lengthMin

최소 길이를 체크합니다. 배열도 사용가능합니다.

```php
<?php
validator()->lengthMin(5)->validate('123456789'); // true
validator()->lengthMin(5)->validate('123456'); // true
validator()->lengthMin(5)->validate('12345'); // true
validator()->lengthMin(5)->validate('1234'); // false

validator()->lengthMin(5)->validate([1, 2, 3, 4, 5]); // true
validator()->lengthMin(5)->validate([1, 2, 3, 4]); // false
```

### lengthMax

최대 길이를 체크합니다. 배열도 사용가능합니다.

```php
<?php
validator()->lengthMax(5)->validate('123'); // true
validator()->lengthMax(5)->validate('1234'); // true
validator()->lengthMax(5)->validate('12345'); // true
validator()->lengthMax(5)->validate('123456'); // false

validator()->lengthMax(5)->validate([1, 2, 3, 4,]); // true
validator()->lengthMax(5)->validate([1, 2, 3, 4, 5]); // true
validator()->lengthMax(5)->validate([1, 2, 3, 4, 5, 6]); // false
```

### email

이메일을 체크합니다.

```php
<?php
validator()->email()->validate('wan2land@gmail.com'); // true
```

`egulias/email-validator`라는 패키지를 사용합니다. 기본값으로 `Egulias\EmailValidator\Validation\RFCValidation`를
사용하고 있습니다. 원한다면 다른 값으로 바꿀 수 있습니다.

```php
<?php
validator()->email(new \Egulias\EmailValidator\Validation\DNSCheckValidation())->validate('wan2land@gmail.com');
```

### regExp

정규식을 체크합니다.

```php
<?php
$validator = validator()->regExp("/^\\d{3,5}$/");

$validator->validate("100"); // true
$validator->validate("1000"); // true
$validator->validate("10000"); // true
```

## 사용자 정의 벨리데이터 (Custom Validator)

`Wandu\Validator\Rules\ValidatorAbstract`객체를 상속받아서 다음과 같이 구현할 수 있습니다.

{% raw %}
```php
<?php
namespace Your\Own\Validators;

use Wandu\Validator\Rules\ValidatorAbstract;

class BetweenValidator extends ValidatorAbstract 
{
    const ERROR_TYPE = 'between:{{min}},{{max}}';
    
    protected $min;
    protected $max;
    
    public function __construct($min, $max)
    {
        $this->min = $min;
        $this->max = $max;
    }
    
    public function test($item)
    {
        return $item >= $this->min && $item <= $this->max;
    }
}
```
{% endraw %}

그리고 다음과 같이 사용할 수 있습니다.

```php
<?php
$validator = new BetweenValidator(10, 20);

$validator->assert(10); // Ok
$validator->assert(11); // Ok
$validator->assert(19); // Ok
$validator->assert(20); // Ok

try {
    $validator->assert(21);
} catch (InvalidValueException $exception) {
    print_r($exception->getTypes()); // ['between:10,20']
}
```

만약에 Exception이 발생하면 Exception의 내용도 `ERROR_TYPE`에 지정한 대로 출력됩니다.

### 사용자 벨리데이터의 네임스페이스 등록하기

벨리데이터의 메서드나, `from`을 통해서 불러오고 싶다면 해당 객체의 네임스페이스를 등록하면 됩니다.

```php
<?php
use Wandu\Validator\ValidatorFactory;

$factory = new ValidatorFactory();
$factory->register("Your\\Own\\Validators");
$factory->setAsGlobal();

```

이제 다음과 같이 사용할 수 있습니다.

```php
<?php
validator()->between(10, 20); // same new BetweenValidator(10, 20)
validator()->from('between:10,20'); // same new BetweenValidator(10, 20)
```

