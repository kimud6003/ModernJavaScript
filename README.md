# ModernJavaScript
(javscript)If you go alone, you go fast, if you go together, you go far

# 프라미스와 async, await

# 1.Callback

> 콜백이란?

- 간단히 이야기 하면 콜백은 다른 함수가 실행을 끝낸 뒤 실행되는 함수입니다.

> 콜백 함수가 필요한 이유는 무엇인가?

- 자바스크립트는 이벤트 기반 언어로 되어있기 때문이다. 자바스크립트는 다음 명령어를 실행하기 전 이전 명령어의 응답을 기다리기보단, 다른 이벤트들을 기다리며 계속 명령을 수행합니다.

> 비동기함수는 무엇인가?

- 기본적으로 자바스크립트는 동기적 함수입니다.

    프로그램에 A, B, C, D의 작업이 있을 때

    A요청 -> A결과 -> B요청 -> B결과 -> C요청 -> C결과 -> D요청 -> D결과 ->  . . .

    와 같이 순차적으로(A-B-C-D) 흘러가는 것입니다. 
 
- 비동기 함수는 이와 반대로

    프로그램의 실행흐름이

    A요청 -> B요청 -> A결과 -> C요청 -> D요청 -> B결과 -> D결과 -> C결과 -> . . .

    와 같이 불규칙하게 나타나는게 특징입니다.

- 그렇다면 순서에 맞지않는 비동기 함수를 왜 사용하는 걸까요? 커피집을 예로 들어보죠

     - 우리는 커피를 주문하면 번호표를 받고 커피가 나올때까지 기다립니다. 그후 커피집 점원은 다른 손님의 커피를 주문을 받고 나중에 커피가 나오면 저에게 커피를 주게 됩니다.  —비동기

     - 우리는 커피를 주문하면 우리뒤에 바로 손님이 있더라도 커피집 점원이 우리에게 커피를 줄때까지 자리를 벗어나지 않고 커피를 기다립니다 —동기

- 이렇게 본다면 동기적으로 작동하면 작업처리가 늦어지는 것을 알수있습니다.

> 콜백함수와 비동기 처리

- 콜백함수와 비동기 처리를 언급하기전에 먼저 가장 대표적인 비동기 함수에 대해 알아 보고가겠습니다.

- src에 있는 스크립트를 읽어 오는 함수 loadScript가 있다고 가정하겠습니다. (이 함수는 비동기 함수입니다)
```js
    function loadScript(src) {
      let script = document.createElement('script');
      script.src = src;
      document.head.append(script);
    }

    loadScript('/my/script.js'); 

    newFunction(); // my/script.js 안에 있는 함수
```
- 이렇게 함수가 있다면 newFunction은 에러가 발생하게 될것입니다. 그 이유는 비동기적으로 작동하기 때문에 my/script.js에 있는 newFunction을 읽기전에 실행 되기 때문입니다.
- 그럼 여기에 콜백 함수를 넣어 보도록 하겠습니다.
```js
    function loadScript(src,testcallback) {
      let script = document.createElement('script');
      script.src = src;

      script.onload = () => testcallback(script); //콜백함수

    document.head.append(script);
    }
```

- 위와 같이 함수를 만들고 아래같이 사용하게 되면  원하는대로  newFunction 함수를 사용할수있습니다.
    
```js
    loadScript('/my/script.js', function() {
      newFunction(); 
      ...
    });
```


# 2.Promise

> 프로미스란?

- 비동기 처리를  위해 콜백 함수를 사용했습니다. 하지만 콜백 함수는 콜백 헬로 비동기 처리 중 발생한 에러의 처리가 곤란하며 여러 개의 비동기 처리를 한번에 처리하는 데도 한계가 있습니다.
- ES6에서는 비동기 처리를 위해 prosmise를 도입했습니다.

> 콜백 헬

- 콜백 헬은 아래 그림과 같이 비동기 처리를 위해 콜백 함수를 사용하면서 순서를 정하기 위해 가독성이 나빠지는 상태를 이야기합니다.
```js
    call1(function(v1) {
      call2(v1, function(v2) {
        call3(v2, function(v3) {
          call4(v3, function(v4) {
            call(v4, function(v5) {
                // v5를 사용하는 처리
            });
          });
        });
      });
    });
```
> Promise 사용

- 프로미스의 사용은 아래와 같습니다.
```js
    // Promise 객체의 생성
    const promise = new Promise((resolve, reject) => {

    if (/* 비동기 작업 수행 성공 */) {
        resolve('result');
      }
      else { /* 비동기 작업 수행 실패 */
        reject('failure reason');
      }   
    });
```
- new Promise에 전달되는 함수는 if문 부터 else까지를 (실행 함수) 라고 부릅니다.
- 실햄함수의 인수 `resolve`와 `reject`는 자바스크립트가 자체적으로 제공하는 콜백입니다. 개발자는 `resolve`와 `reject`를 신경 쓰지 않고 executor 안 코드만 작성하면 됩니다.
- 대신 executor에선 결과를 즉시 얻든, 늦게 얻든 상관없이 상황에 따라 인수로 넘겨준 콜백 중 하나를 반드시 호출해야 합니다.
- `resolve(value)` — 일이 성공적으로 끝난 경우, 그 결과를 나타내는 `value`와 함께 호출
- `reject(error)` — 에러 발생 시 에러 객체를 나타내는 `error`와 함께 호출

> 프로미스의 상태 

| 프로미스 상태  | 의미  | 구현  |
|:----------|:----------:|----------:|
| pending | 비동기 처리가 아직 수행되지 않은 상태 | reslove 또는 reject 함수가 아직 호출되지 않은 상태 |
| fulfilled | 비동기 처리가 수행되고 성공| resolve 함수가 호출된 상태 |
| rejected | 비동기 처리가 수행되고 실패  | reject함수가 호출된 상태 |
| settled | 비동기 처리가 수행된 상태 성공 or 실패  | resolve 또는 reject 함수가 호출된 상태 |

> 프로미스의 후속 처리 메소드

- Promise로 구현된 비동기 함수는 Promise 객체를 반환하여야 합니다. 따라서 함수를 호출하는 측에서는 Promise 객체의 후속 처리 메소드 (then, chatch,finally)를 통해 비동기 처리 결과를 전달 받아 처리 할수 있습니다.
- then 사용법
```js
  let promise = new Promise(function(resolve, reject) {
    setTimeout(() => resolve("성공"), 1000);
  });

  promise.then(
    result => alert(result), // 성공을 출력
    error => alert(error) // 실행되지 않음
  );
```
  - then 메소드는 두개의 콜백 함수를 전달 받게 되는데요. 첫번째 인자는 성공시 호출, 두번쨰 인자는 실패시에 호출됩니다.

- catch 사용법 
```js
  let promise = new Promise((resolve, reject) => {
    setTimeout(() => reject(new Error("에러 발생!")), 1000);
  });

  // .catch(f)는 promise.then(null, f)과 동일하게 작동합니다
  promise.catch(alert); // 1초 뒤 "Error: 에러 발생!" 출력
```
  - catch 메소드는 예외 처리를 할때 사용하게 됩니다.(then에 실패와는 다릅니다!)


# 프로미스 체이닝
  > 프로미스 체이닝이란?

- 비동기 처리를 위해 callback을 사용했더니 callback헬이 발생 했던것이 기억이 날것입니다. 프로미스에서는 이러한 코드의 복잡도를 줄어들게 하기 위해서 후속처리 메소드를 연결(chaing)하여 여러 개의 프로미스를 연결 하여 사용합니다.

- 프로미스 체이닝 코드
```js
  new Promise(function(resolve, reject) {

    setTimeout(() => resolve(1), 1000); // (*)

  }).then(function(result) { // (**)

    alert(result); // 1
    return result * 2;

  }).then(function(result) { // (***)

    alert(result); // 2
    return result * 2;

  }).then(function(result) {

    alert(result); // 4
    return result * 2;

  });
```
- 프라미스 체이닝은 result가 .then 핸들러의 체인(사슬)을 통해 전달된다는 점에서 착안한 아이디어입니다.

- 위 예시는 아래와 같은 순서로 실행됩니다.
> 1초 후 최초 프라미스가 이행됩니다, – (*)
  이후 .then 핸들러가 호출됩니다. –(**)
  2에서 반환한 값은 다음 .then 핸들러에 전달됩니다. – (***)
  이런 과정이 계속 이어집니다.
  result가 핸들러 체인을 따라 전달되므로, alert 창엔 1, 2, 4가 순서대로 출력됩니다.

- 그렇다면 여기서 문제를 내도록 하겠습니다
``` js
  new Promise(function(resolve, reject) {

    setTimeout(() => resolve(1), 1000);

  }).then(function(result) {

    alert(result); // 결과 값은?

    return new Promise((resolve, reject) => { 
      setTimeout(() => resolve(result * 2), 1000);
    });

  }).then(function(result) { 

    alert(result); // 결과 값은?

    return new Promise((resolve, reject) => {
      setTimeout(() => resolve(result * 2), 1000);
    });

  }).then(function(result) {

    alert(result); // 결과 값은?

  });
```
- return 값으로 프로미스를 반환 하였습니다. 이럴 경우 값은 어떻게 나올까요?
- 결과는 위에 코드와 동일하게 1,2,4를 반환하게 됩니다. 처음 promise에서 반환을 새로은 promise로 해버리기 때문이죠

# Promise 정적 메서드 
> Promise API란?
- Promise 클래스에는 5가지 정적 메서드가 있습니다. 이번 챕터에선 다섯 메서드의 유스 케이스에 대해서 빠르게 알아보겠습니다.

> Promise.all
- Promise.all은 요소 전체가 프로미스로 담겨있는 배열을 인자로 전달 받고 새로운 프로미스를 반환합니다.

```js
  Promise.all([
    new Promise(resolve => setTimeout(() => resolve(1), 3000)), // 1
    new Promise(resolve => setTimeout(() => resolve(2), 2000)), // 2
    new Promise(resolve => setTimeout(() => resolve(3), 1000))  // 3
  ]).then(alert); // 프라미스 전체가 처리되면 1, 2, 3이 반환됩니다. 각 프라미스는 배열을 구성하는 요소가 됩니다.

  //or

  Promise.all([
    new Promise((resolve, reject) => {
      setTimeout(() => resolve(1), 1000)
    }),
    2,
    3
  ]).then(alert); // 1, 2, 3
```
- 여기서 주의 해야할점은 결과값이 [1,2,3]이 출력 된다는 점 입니다. 처리 순서는 3,2,1인데도 말이죠.
- 만약 여기서 2을 반환하는 프로미스 함수가 에러로 인해 중간에 끊긴다면 어떻게 될까요? Promise.all 전체가 취소가 됩니다.

> Promise.allSettled

-  Promise.all은 프라미스가 하나라도 거절되면 전체를 거절합니다, 하지만 Promise.allSettled는 모든 프라미스가 처리될 때까지 기다립니다.
- 예를 들어 여러 사람의 정보를 가져오고 있다고 해봅시다. 여러 요청 중 하나가 실패해도 다른 요청 결과는 여전히 있어야 합니다. 이럴 경우 Promise.allSettled를 사용하게 됩니다.

``` js
  let urls = [
    '사람관련 링크',
    '동물관련 링크',
    '링크가 아닌 이상한 문자들'
  ];

  Promise.allSettled(urls.map(url => fetch(url)))
    .then(results => { 
      results.forEach((result, num) => {
        if (result.status == "fulfilled") {
          alert(`${urls[num]}: ${result.value.status}`);
        }
        if (result.status == "rejected") {
          alert(`${urls[num]}: ${result.reason}`);
        }
      });
    });
```
- 만약 위와 같이 코드를 짰다면 `사람관련 링크`와  `동물관련 링크` 에서는 `fetch`가 작동하지만 `링크가 아닌 이상한 문자들`에서 는 에러가 발생 하게 될것입니다.

- 결과
```js
  [
    {status: 'fulfilled', value: ...응답...},
    {status: 'fulfilled', value: ...응답...},
    {status: 'rejected', reason: ...에러 객체...}
  ]
```

> Promise.race

- Promise.race는 Promise.all과 비슷합니다. 다만 가장 먼저 처리되는 프라미스의 결과(혹은 에러)를 반환합니다.

```js
  Promise.race([
    new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
    new Promise((resolve, reject) => setTimeout(() => reject(new Error("에러 발생!")), 2000)),
    new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
  ]).then(alert); // 1
```

> Promise.resolve/reject

- Promise.resolve와 Promise.reject 메소드는 존재하는 값을 Promise로 래핑하기 위해 사용합니다. 하지만 async/await이 생김에 따라 요즘은 사용하지 않습니다.

```js
let promise = new Promise(resolve => resolve(value));
let promise = new Promise((resolve, reject) => reject(error));
```

# Promisification
- 콜백을 받는 함수를 프라미스를 반환하는 함수로 바꾸는 것을 '프라미스화(promisification)'라고 합니다.
- 콜백 기반의 라이브러리가 있을때 프로미스화 해야하는 경우가 종종 있다고 합니다.

```js
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`${src}를 불러오는 도중에 에러가 발생함`));

  document.head.append(script);
}
```

- 위 콜백함수를 프로미스화를 해보겠습니다.

```js
let loadScriptPromise = function(src) {
  return new Promise((resolve, reject) => {
    loadScript(src, (err, script) => {
      if (err) reject(err)
      else resolve(script);
    });
  })
}
```
- 단 프로미스화는 콜백을 완전히 대채하지는 못합니다.프로미스는 하나의 결과만 가질 수 있지만, 콜백은 여러 번 호출을 할수 있기 때문입니다. 따라서 <b>프라미스화는 콜백을 단 한 번 호출하는 함수에만 적용하시기 바랍니다. 프라미스화한 함수의 콜백을 여러 번 호출해도, 두 번째부터는 무시됩니다.</b>

# async와 await

