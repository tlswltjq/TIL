# Mock과 Stub에 대해서 알아보자

## 행위검증과 상태검증

```java
boolean insertData(Data data){
    //아마도 데이터 저장
    return true|false;
}
```

`insertData()`의 결과는 시그니처에의해 boolean타입일 것이고, 실제 실행 결과로 t/f인지 확인하는 것이 행위검증

insertData는 무엇을 할까?
구현에 따라 달라지겠지만 data를 insert할 것 이다.

`insertData()`이 순수 함수가 아니고선 insert를 하는 과정에서 부수적인 side effect가 일어날 것이다.

> side effect : 함수가 반환값 이외에 외부 상태나 데이터에 영향을 미치는 모든 행동<br>e.g. 예외를 핸들링, 전역/ 외부의 변수값을 변경, 콘솔 출력, 외부 자원(파일/ 데이터베이스/ 네트워크)에 접근하여 데이터를 수정

```java
boolean insertData(Data data){
    repository.save(data);
    return true|false;
}
```
저장이 잘 되었을까? `repository.save(data)`이 잘 수행 되었을까?<br>data.isPersist == true인지 검증하는 것이 상태검증

이 때 상태검증시 `MOCK`이 강제되며 행위검증은 `STUB`으로도 검증 가능하다.
## Stubbing
테스트 대역에 특정 메소드의 호출과 응답을 작성하는 것
## MOCK

## STUB