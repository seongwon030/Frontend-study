## key에 index 값을 넣는다면

- 항목 삽입, 삭제, 재정렬 시 렌더링 순서가 시간이 지나면서 달라짐
- index 값 이외에 즉석에서 값을 생성할 때도 마찬가지
- key는 실제로 컴포넌트에 전달되지 않고 리액트에서만 사용


## React 렌더링 과정

workInProgress에 모든 변경사항 반영되면 current와 workInProgress 비교하여 실제 DOM에 반영할 리스트인  

**이펙트 리스트**를 수집할 수 있다. 이 리스트의 요소만 실제 DOM에 반영해 화면이 변하게 된다. 

current를 참조하는 트리는 workInProgress로 바뀐다.

## Key의 역할

### 1. 가상 DOM의 key prop

리액트는 가상 DOM 객체를 메모리상에 생성한다. 리액트 16 이상에서 Fiber Node의 구조는 아래와 같다.

```typescript
function FiberNode(
  tag: WorkTag;
  pendingProps: mixed,
  key: null | string,
  mode: TypeOfMode,
) {
  this.tag = tag;
  this.key = key;
  this.elementType = null;
  this.type = null;
  this.stateNode = null;
}
```

### 2. 재조정 과정에서 Key 역할

두 개 트리 비교 시 최첨단 알고리즘 사용해도 n개 엘리먼트에 대해 O(n3) 시간복잡도.

1000개 엘리먼트를 위해 10억 번 비교.. 이는 굉장히 비쌈.


```
1. 타입이 다른 두 엘리먼트는 서로 다른 트리를 만든다.
2. 개발자가 key prop을 전달해 어떤 자식 엘리먼트가 변경되지 말아야 할지 표시한다.
```

1번에서 타입은 엘리먼트 타입. (div가 span으로 바뀌거나 컴포넌트가 달라질 때) 
**타입이 바뀌지 않는다면 변경된 속성만 갱신.**

2번 -> key prop 바뀌면 실제 데이터 변경되지 않아도 리렌더링 된다.
key 사용함으로써 key값 변하지 않는다면 연산을 다시 진행하지 않고, 이미 있는 값 재활용. 
=> `불필요한 연산을 방지하며 성능 최적화`

## index 넣거나 즉석에서 값 생성 시


### key에 배열 index 전달

javascript 배열 index 값은 각 요소 순서 번호. 배열의 끝이 아닌 중간이나 처음에 값을 더하거나 삭제하면 모든 index 값이 달라진다.

=> index가 바뀐 리스트로 다시 연산이 이뤄져야 한다.

### key에 즉석 생성값 전달

index 사용보다 비효율적. 사실상 매번 모든 연산을 다시 해야 한다. 

## 정리

- 리액트는 key prop으로 리스트내의 아이템을 다시 연산할지 말지 결정한다.
- key prop에 index 넣을 시 배열 순서 바뀔 때마다 다시 연산해야 한다.
- key prop에 즉석에서 생성한 랜덤값 넣을 시 매번 key prop이 달라져 연산도 다시 해야 한다.

key prop에는 고유값 전달하여 불필요 연산을 막아야 한다.


### 무조건 index 금지 ?

배열 요소가 추가, 삭제되거나 순서가 달라지지 않는 고정 데이터라면 사용해도 상관없음. 

index값이 고정이니까 연산도 불필요.


