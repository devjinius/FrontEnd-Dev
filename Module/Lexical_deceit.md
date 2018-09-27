# Lexical deceit

주변에서 `eval()`은 사용하면 안된다고들 말한다.
</br>

자바스크립트에는 **Lexical Scope** 라는 개념이 있다.이를 알기 위해선 scope에 대한 정의가 필요하다.

</br>
</br>

## 스코프와 렉시컬 스코프란

스코프란 자바스크립트 엔진이 Identifier(확인자, 구별자)로 현재 스코프 혹은 그 상위의 스코프 내에서 변수를 찾아내는(LHS, RHS) 규칙을 얘기한다.
</br>

쉽게말해 자바스크립트 엔진이 변수를 찾는 규칙이다. 이미 알고있는 지식이다. 지식을 늘려보자.
</br>

자 이제 렉시컬 스코프를 이해하려면 자바스크립트의 컴파일에 대해 조금 알아야 한다.
</br>

기본적으로 자바스크립트는 컴파일 언어다. 컴파일에는 크게 세가지 단계가 존재한다.
</br>

1. 토크나이징/렉싱 - 코드를 잘게 나누어 토큰으로 만든다.
2. 파싱 - 나눈 토큰을 의미있게 AST(Abstract Syntax Tree)라는 트리로 만든다.
3. 코드생성 - AST를 기계어로 만든다.

**렉시컬 스코프란 저 1단계에서 발생하는 즉, 렉싱 과정에서 정의되는 스코프를 말한다.** 무슨 말이냐 하면 프로그래머가 변수와 스코프 블록을 어떻게 구성하는냐에 따라 컴파일의 렉싱 타임에서 정의되는 스코프 방식을 Lexical Scope라고 한다.

</br>
</br>

## 렉시컬 속이기

어려운 얘기였지만 쉽게 말하면 프로그래머가 코드를 어떻게 작성하는지에 따라 엔진이 판단하여 스코프를 만드는게 렉시컬 스코프다.
</br>

***즉 컴파일 과정에서 발생한다.***
</br>

그런데 이를 런타임 과정에서 수정하게 되면 렉시컬을 속일 수 있다.
</br>

방법은 크게 두가지다. `Eval`과 `With`가 있다.
</br>

With의 경우 최근 거의 사용하지 않는다. 다만 Eval 은 옛날 코드에서 종종 발견된다.
</br>

단순히 Eval을 사용하지 말자가 아니라 왜 사용하면 안되는지에 대해 조금 깊숙하게 알아보자.

</br>
</br>

### Eval

`eval()` 함수는 받아들인 파라미터를 마치 코드처럼 실행하는 함수다. 말로하면 어렵다. 직접 코드를 보자.

```javascript

    function foo(str, a) {
        eval(str);
        console.log(a, b);
    }

    var b = 2;
    foo("var b = 3;", 1); // result  1, 3
```

여기서 `var b = 3;`은 마치 원래 있던 코드처럼 실행되고 이 코드가 새로운 변수 b를 선언하고 초기화하며 이미 존재하는 foo()의 렉시컬 스코프를 수정하게 된다. 
</br>

즉 foo()안에 변수 b를 만들어 바깥 스코프의 변수 b를 가리게 된다. 이를 Shadowing 이라고 한다. 내부의 스코프의 변수가 같은 이름이 바깥의 스코프의 변수를 가리는 현상을 말한다.
</br>

렉시컬 스코프의 기본 정의는 프로그래머가 코딩을 할 때 정한 스코프라는 점이다.
</br>

eval()과 같은 함수는 이미 정의된 스코프를 런타임 시 수정할 수 있게 된다. 즉 정의를 흔드는 행위다.

이와 비슷한 효과를 내는 함수로는 `setTimeout(), setInterval()`과 같은 함수들이 있다. 또한 `new Function()`도 비슷한 방식으로 동적으로 렉시컬 스코프를 변경한다.
</br>

개념은 조금씩 다르지만 근본적으로 이 함수들의 공통점은 렉시컬 스코프를 변경할 수 있다는 점이다.

</br>
</br>

## 결론

흔히들 주변에서 `eval()`을 사용하면 안된다고들 알려준다. 뭐 성능이 안좋다라는 이야기들을 들을 수 있다. 그런데 프로그래머라면 항상 의심해봐야 한다. 진짜 안좋을까?
</br>

장점도 물론 있다. 코딩의 유연성? 같은 요소들 말이다.
</br>

그렇지만 **단점이 너무 치명적이다.** 자바스크립트는 다시 말하지만 컴파일 언어다. 이 컴파일 언어는 컴파일 단계에서 최적화 작업을 진행한다. 예를들어 렉시컬 스코프를 만들어 Identifier를 미리 확인하여 추후 Identifier 검색을 빠르게 만든다.
</br>

그런데 eval()과 같이 이 렉시컬 스코프를 뒤흔들 수 있는 함수를 사용하게 된다고 하자. 그러면 이미 확인했던 Identifier의 위치가 달라질 수 있다는 점이다. 즉 기존에 진행했던 최적화작업이 확실하지 않을 수 있게 된다는 점이다. 불확실한 작업을 할 수는 없다. 즉 하나마나한 최적화 작업이 되어버린다.
</br>

즉 엔진이 프로그래머가 작성한 코드를 바탕으로 렉시컬 스코프를 만든다. 이를통해 최적화 작업을 수행한다. 그런데 `eval()`을 사용하게 되면 이 렉시컬 스코프를 수정할 수 있게된다. 이러한 가정 때문에 최적화 작업은 무효화 된다. 따라서 `eval()`과 같은 함수는 사용하지 않는것이 적절하다.