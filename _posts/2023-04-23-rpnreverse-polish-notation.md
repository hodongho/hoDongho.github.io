---
title: RPN(Reverse Polish Notation)이란?
last_modified_at: '2023-04-23'
tags:
- RPN
- Stack
- ReversePolishNotation
- PostfixNotation
categories:
- 용어
description: RPN이란?
---


![출처 위키백과](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a6/Postfix-dia.svg/125px-Postfix-dia.svg.png)
_출처 위키백과_

RPN이란 Reverse Polish Notation의 약자로 역폴란드 표기법이라고 합니다.  연산자를 수식 뒤쪽에 쓰는 표기법으로써 후위 표기법(postfix notation)이라고 하기도 합니다.

> 💡 여기서 Polish는 폴란드의 논리학자인  얀 우카시에비치(Jan Łukasiewicz)가 고안한 폴란드 표기법(Polish Notation, 전위 표기법으로 불리기도 합니다)에서 유래됐습니다.


일반적으로 수식 계산을 할 때 사용하던 연산자를 수의 중간에 쓰는 중위 표기법(1 + 2와 같은)과 달리 후위 표기법이라는 이름에서 볼 수 있듯이 RPN은 연산자가 뒤에 들어가게 됩니다.

## 예시

> 1 + 2 - 3

다음과 같은 수식이 있을 때 이 수식을 RPN으로 쓰면 다음과 같습니다.

> 1 2 + 3 -

## RPN을 쓰는 이유
RPN을 쓰는 이유는 우선순위를 신경쓰지 않아도 되기 때문입니다.
우리가 평소에 사칙연산을 할 때 사용하는 중위 표기법의 경우 연산의 우선순위를 고려해야하기 때문에 계산하는데 있어서 추가적인 처리가 필요합니다. 그러나 RPN 즉 후위 표기법의 경우 연산 우선순위와 상관없이 순서대로 계산하면 되기 때문에 추가적인 처리가 필요 없습니다.

따라서 비교적 빠른 연산이 가능해집니다.  그리고 스택을 활용하여 쉽게 구현할 수 있습니다.

내부적으로 RPN을 사용하는 프로그램들(Forth, STOIC, etc.)을 종종 만날 수 있습니다.

## Stack을 활용한 구현
간단하게 1 + 2 - 3을 Stack을 활용해 구현해보겠습니다.

RPN을 사용하여 나타내면 1 2 + 3 - 가 됩니다.

로직은 간단히 숫자가 나오면 스택에 넣어주고 연산자가 나오면 스택의 상위 값 2개를 꺼내 연산을 해주고 다시 넣어주면 됩니다.


| 스택 | 연산식 | 설명 |
| -------- | -------- | -------- |
| 1 | 2 + 3 - | 스택에 1을 넣어줍니다 |
| 2<br>1 | + 3 - | 스택에 2를 넣어줍니다 |
|  | + 3 - | 연산자가 나왔으니 스택의 상위 값 2개를 빼서 연산해줍니다 2 + 1 = 3 |
| 3 | 3 - | 연산 결과를 스택에 넣어줍니다 |
| 3<br>3 | - | 스택에 3을 넣어줍니다 |
|  | - | 스택에서 값을 빼서 - 연산을 해줍니다 3 - 3 = 0 |
| 0 |  |연산 결과를 스택에 넣어줍니다. |

다음과 같이 스택을 활용하여 RPN 계산을 할 수 있습니다.

## Reference

[Reverse_Polish_notation](https://en.wikipedia.org/wiki/Reverse_Polish_notation)<br>
[Reverse Polish notation](https://www.joinc.co.kr/w/man/12/ReversePolishNotation)
