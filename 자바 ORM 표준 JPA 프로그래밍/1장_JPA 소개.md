# JPA 소개
## SQL을 직접 다룰 때 발생하는 문제점
데이터 베이스에 데이터를 관리하기위해서는 SQL을 사용<br>
자바 애플리케이션은 JDBC API를 사용해 SQL을 데이터베이스에 전달한다.
<img width="637" alt="image" src="https://github.com/tlswltjq/TIL/assets/41179427/a6dcd5a2-f9e7-431a-b25d-44f6a364f260">
### 반복반복반복
CRUD를 개발하는 과정에을 살펴보자
```java
// 회원 객체
public class Member{
    private String memberId;
    private String name;
    ...
}
```
```java
//회원용 DAO
public class MemberDAO{
    public Member find(String memberId){...}
    public void save(Member member){...}
}
```
각 기능은 다음 순서대로 개발이 진행될것이다.

`public Member find(String memberId)`
1. 회원 조회용 SQL을 작성
2. JDBC API를 사용해 SQL을 실행
3. 조회 결과를 Member 객체로 매핑

`public void save(Member member){...}`
1. 회원 등록용 SQL을 작성
2. 회원 객체의 값을 꺼내 등록 SQL에 전달.
3. JDBC API를 사용해 SQL을 실행

>수정 및 삭제기능 또한 SQL을 작성하고 JDBC API를 이용하는 반복적인 작업이 될 것

회원 정보를 자바 컬렉션에 보관한다면 간단하게 저장할 수 있을것
```java
list.add(member)
```
그러나 데이터베이스는 객체 구조와 다른 데이터 중심의 구조를 가져, 객체를 직접 데이터베이스에 저장하거나 조회할 수 없어 중간에서 JDBC API와 SQL을 이용해 변환해 주어야 하는 것이다.

>데이터베이스에 CRUD하기위해서 많은 SQL과 JDBC API를 코드로 작성 * 테이블수 = 지옥
### SQL에 의존적인 개발
회원 객체는 데이터베이스에 저장되어있다. 회원의 전화번호를 추가적으로 관리하기위해 객체에 컬럼을 추가하게된다면?
```java
public class Member {
    private String memberId;
    private String name;
    private String tel;     //추가
}
```
객체가 변경됨에 따라 등록, 조회등의 기능의 SQL과 JDBC API를 수정해야한다.

회원 객체가 자바 컬렉션에 보관되었다면 `.add()`, `.get()`, `.set()`등을 사용해 많은 코드를 새롭게 작성하거나 수정할 필요가 없었을 것.

연관객체가 추가되고 복잡해지면 문제상황 발생시 DAO의 SQL을 확인해야 원인을 알 수 있다.

#### 애플리케이션에서 SQL을 직접 다룰 때 발생하는 문제점
>엔티티 : 비즈니스 요구사항을 모델링한 객체
- 진정한 의미의 계층분할이 어렵다.
- 엔티티를 신뢰할 수 없다.
- SQL에 의존적인 개발을 피하기 어렵다.
### JPA와 문제해결
JPA가 앞선 문제를 해결하기위한 API를 살펴보자
```java
//저장
jpa.persist(member)
```
`persist()`메서드는 객체를 데이터베이스에 저장한다.<br>메서드가 호출되면 JPA가 매핑 정보를 보고 적절한 INSERT SQL을 생성해 데이터 베이스에 전달한다.
```java
//조회
String memberId = "helloId";
Member member = jpa.find(Member.class, memberId);
```
`find()`메서드는 객체 하나를 데이터베이스로부터 조회한다.<br>메서드가 호출되면 JPA가 매핑 정보를 보고 적절한 SELECT SQL을 생성해 데이터 
```java
//수정
Member member = jpa.find(Member.class, memberId);
member.setName("새 이름");
```
JPA는 수정 메소드를 제공하지 않으나 객체를 조회하고 값을 변경하면 트랜잭션을 커밋할 때 적절한 UPDATE SQL이 전달된다.
```java
//연관된 객체 조회
Member member = jpa.find(Member.class, memberId);
Team team = member.getTeam();
```
연관된 객체를 사용하는 시점에서 적절한 SELECT SQL을 실행한다.
## 패러다임의 불일치
애플리케이션은 발전하며 내부적인 복잡성이 점점 커지게 된다.<br>객체지향 프로그래밍을 통해 도메인 모델을 객체로 모델링 하게되면 객체지향 프로그램에서 제공하는 기능을 통해 시스템의 복잡성을 제어할 수 있다.

그러나 인스턴스가 생성되면 메모리가 아닌 어느곳에 <b>영구적</b>으로 저장해야한다.

객체는 속성과 기능을 가진다. 기능은 클래스로 정의되어 속성만 저장하여 불러와 복구하면 된다.<br>상속, 참조를 통해 객체가 복잡해져 갈 수록 저장하기 쉽지 않아진다.<br>자바는 이를 고려해 객체를 파일로 저장하고 복구할 수 있도록 직렬화, 역직렬화 기능을 지원하지만 객체를 검색하기 어렵다는 문제가 존재한다.

데이터베이스에 객체를 저장하는것이 현실적인 대안이지만 데이터 중심으로 구조화된 관계형 데이터베이스는 추상화, 상속, 다형성과 같은 개념이 없기에 기능과 표현 방법이 다르다.

객체 구조를 테이블 구조에 저장하는 데는 한계가 있다.