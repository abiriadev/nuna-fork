# nuna
(우리의 가상) 누나 언어 v0.4

## 명세
### 값 (value)
누나 언어에는 `부호있는 정수형(signed integer)` 값만이 존재합니다.\
값의 양수 및 음수 범위는 구현체가 임의로 설정할 수 있으나, 오버플로 및 언더플로 동작은 정의되지 않습니다.

### 스택 (stack)
누나 언어의 런타임은 단 하나의 스택을 갖습니다.\
내부 변수를 제외한 모든 변수는 이 스택에 저장됩니다.

스택의 `스택 번호(index)`는 `양의 정수(unsigned integer)`이며, `1`부터 시작합니다.\
스택의 용량은 기본적으로 무한(unlimited)하다고 가정하며, 실제 용량은 구현체별로 다를 수 있습니다.

스택은 일련의 `아이템`으로 이루어져 있으며, 각 `스택 번호`는 하나의 `아이템`을 가리킵니다.\
각 `아이템`은 임의의 `값(value)`을 가리키거나 `null`일 수 있으며,\
프로그램이 처음 시작할 때 스택의 모든 아이템은 `null`로 초기화 됩니다.

### 변수 가리키기 (variable pointer)
누나 언어의 런타임은 `pointer`라는 내부 변수를 갖습니다.\
이는 현재 가리키고 있는 `스택 번호`를 의미합니다.\
프로그램이 처음 시작할 때 `pointer`값은 `0`으로 초기화됩니다.
이는 어떠한 `아이템`도 가리키고 있지 않음을 의미합니다.\

`pointer`와 `스택 번호`가 같은 `아이템`을 `현재 값`이라고 합니다.\
`pointer - 1`과 `스택 번호`가 같은 `아이템`을 `이전 값` 혹은 `앞의 값`이라고 합니다.

### 키워드 (keywards / operations)
#### `눈`, `누`
`pointer`값을 1만큼 증가시킨 후 스택의 `현재 값`에 `1`을 대입합니다.\
뒤에 `.`이 있을 경우 `.`의 개수만큼을 대입합니다. [예제](examples.md#눈-누-예제)

#### `난`, `나`
`현재 값`에 `1`을 곱합니다.\
뒤에 `.`이 있을 경우 `.`의 개수만큼을 곱합니다. [예제](examples.md#난-나-예제)

#### `주`
`현재 값`에서 `1`을 뺍니다.\
뒤에 `.`이 있을 경우 `.`의 개수만큼을 뺍니다. [예제](examples.md#주-예제)

#### `거`
`현재 값`에 `1`을 더합니다.\
뒤에 `.`이 있을 경우 `.`의 개수만큼을 더합니다. [예제](examples.md#거-예제)

#### `헤`
`POP` 작업을 수행합니다.\
이때 `POP`작업이란 `현재 값`에 `null`을 대입하고
`pointer`값을 1만큼 감소시키는 것을 의미합니다.\

이 작업은 스택의 길이를 `1`만큼 줄입니다.\
만약 `pointer`의 값이 이미 `0`이라면, `OutOfStackRange` 에러를 발생시킵니다. [예제](examples.md#헤-예제)

#### `으`
`이전 값`을 의미합니다.\
`.` 대신 사용할 수 있습니다. [예제](examples.md#으-예제)

#### `응`
`이전 값`에서 `현재 값`을 뺀 결과를 `현재 값`에 대입합니다.\
그 후, `이전 값`에 `null`을 대입합니다. [예제](examples.md#응-예제)

#### `흐`
`현재 값`에 `1`을 제곱합니다.\
뒤에 `.`이 있을 경우 `.`의 개수만큼을 거듭제곱합니다.\
`흐`로 시작한 문장은 `읏`으로 끝나야 합니다. [예제](examples.md#흐-읏-예제)

#### `읏`
아무런 행동을 하지 않습니다.\
`흐`로 시작한 문장을 끝낼때 사용합니다.

#### `💕`
`이전 값`에 `현재 값`을 더한 결과를 `현재 값`에 대입합니다.\
그 후, `이전 값`에 `null`을 대입합니다. [예제](examples.md#-예제)

#### `!`
`현재 값`을 유니코드 문자로 해석하여 UTF-8 인코딩 출력합니다.\
만약 `현재 값`을 유니코드 문자로 해석할 수 없자면, `OutOfUnicodeRangeError`를 발생시킵니다.\
대부분의 구현체가 출력으로 stdout을 사용합니다.

#### 그 외
`.`와 `으`를 요구하지 않는 키워드에서 `.`와 `으`를 사용할 경우 `.`과 `으`는 무시됩니다. [예제](examples.md#외1-예제)

`.`와 `으`를 요구하는 키워드에서 `.`와 `으`가 사용되지 않은 경우 `1`으로 간주합니다.

`현재 값`을 요구하는 키워드를 사용할 때 `pointer`의 값이 `0`인 경우, 또는 `현재 값`에 `null`이 들어있을 경우 `현재 값`은 `0`으로 간주합니다.

`이전 값`을 요구하는 키워드를 사용할때 `pointer` 값이 `0`또는 `1`인 경우, 또는 `이전 값`에 `null`이 들어있을 경우 `이전 값`은 `0`으로 간주합니다.  [예제](examples.md#외2-예제)

### 에러 (error)
누나 언어는 프로그램 실행 중 다룰 수 없는 상황이 발생하였을 때 다음과 같은 에러를 발생시킵니다.

#### 문법 에러
- `SyntaxError`
  - `눈` `누`, `난`, `나`, `주`, `거`, `.`, `헤`, `으`, `응`, `흐`, `읏`, `💕`, `!`가 아닌 다른 문자가 사용되었을 때 발생합니다.
  - `흐`로 시작한 문장이 `읏`으로 끝나지 않았을 때에도 발생합니다.

#### 런타임 에러
- `OutOfValueRange`: 구현체별로 정해진 `값`의 상한 또는 하한을 초과하는 연산을 실행했을 때 발생합니다. 이는 구현체에 따라 구현하지 않을 수 있습니다.
- `OutOfStackRange`: 구현체별로 정해진 `스택`의 상한 또는 하한 용량을 초과하는 연산을 실행했을 때 발생합니다.\
  상한 용량을 초과하는 경우에 대해서는 구현체에 따라 구현하지 않을 수 있지만,\
  하한 용량을 초과하는 경우에 대해서는 반드시 본 에러를 발생시켜야 합니다. 이는 주로 `POP` 작업을 수행할 때 발생합니다.
- `OutOfUnicodeRangeError`: `!`명령에서 `현재 값`이 음수이거나 `1,114,111`보다 클 경우 발생합니다.

## 예제
```
눈나..흐.....읏..나주..거....흐...읏...
누..나..나...흐....읏..나주..거....💕
눈나.....나..흐...읏나.....주거...💕
누나..흐..읏나.......주..거......응읏..!

눈나..으흐읏
누으나.....주..흐....읏나....응
누나.....나..주...읏나......응!
```
* @pmh-only님이 제작하셨습니다
* 출력 결과: 누나

## 구현체
2021년 3월 13일 기준 구현체 목록입니다.
* [nunalang/web-nuna](https://github.com/nunalang/web-nuna) - [공식] 웹누나: 바닐라 JS Nuna 구현체
* [hui1601/nuna-interpreter](https://github.com/hui1601/nuna-interpreter) - [비공식] C++ Nuna 인터프리터
* [pl-Steve28-lq/PyNuna](https://github.com/pl-Steve28-lq/PyNuna) - [비공식] Python Nuna 인터프리터
* [franknoh/nuna-interpreter](https://github.com/franknoh/nuna-interpreter) - [비공식] nodejs Nuna 인터프리터

## 릴리즈 목록 (releases)
| tag | status | released at | contributors | main commit |
|:---:|:------:|:-----------:|:------:|:------------|
| v0.4 | `stable` | 2021.03.17 | @pmh-only, @Akiacode | a4c922d |
| v0.3 | `stable` | 2021.03.07 | @hui1601, @pmh-only, @Akiacode, @franknoh, @pl-Steve28-lq | 0ee138b |
| v0.2 | `outdated` | 2021.02.23 | @AkiaCode | 96f8362 |
| v0.1 | `unsupported` | - | - | - |
