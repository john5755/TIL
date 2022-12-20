# This

- 대부분의 경우 함수를 호출한 방법에 의해 결정

- 실행 중에는 할당으로 설정할 수 없고, 함수를 호출할 때 마다 다를 수 있음

- `bind`: 함수를 어떻게 호출했는지 상관하지 않고 this 값을 설정

- `->`: 스스로의 this binding을 제공하지 않음

```js
const test = {
  prop: 42,
  func: function() {
    return this.prop;
  },
};

console.log(test.func());
// expected output: 42

```


