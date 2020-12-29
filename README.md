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

    ***script.onload = () => testcallback(script); //콜백함수***

    document.head.append(script);
    }
``
    위와 같이 함수를 만들고 아래같이 사용하게 되면  원하는대로  newFunction 함수를 사용할수있습니다.
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
- 실햄함수의 인수 resolve와 reject는 자바스크립트가 자체적으로 제공하는 콜백입니다. 개발자는 resolve와 reject를 신경 쓰지 않고 executor 안 코드만 작성하면 됩니다.
- 대신 executor에선 결과를 즉시 얻든, 늦게 얻든 상관없이 상황에 따라 인수로 넘겨준 콜백 중 하나를 반드시 호출해야 합니다.
- `resolve(value)` — 일이 성공적으로 끝난 경우, 그 결과를 나타내는 `value`와 함께 호출
- `reject(error)` — 에러 발생 시 에러 객체를 나타내는 `error`와 함께 호출

[프로미스의 상태](https://www.notion.so/88cfe885491a4ea4987f8fa2c4735032)
