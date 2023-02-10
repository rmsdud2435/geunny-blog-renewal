---
layout: default
title: "@Transaction 이해하기"
parent: SpringBoot
grand_parent: Spring/SpringBoot
permalink: /spring/springboot/annotation/1
nav_order: 96
---

## 들어가기 앞서...

 카카오뱅크 과제를 풀면서 @transaction을 처음 접하게 되었다. 내가 업무적으로 맡은 소스들에서는 만약 트랜잭션 처리가 필요하다면 보통 XML파일에서 JDBC설정에서 transaction 허용 설정 후 Sqlsession 클래스를 활용하여 처리하였다. 과제에서 어노테이션과 어노테이션의 옵션을 활용하여 보다 편하게 처리하는 것을 보고 @transaction 활용법을 공부해보자는 생각이 들었다.


## 트랜잭션이란?

 https://kafcamus.tistory.com/30의 블로그에 DB에 대해 전혀 모르더라도 이해할 수 있게 쉽게 설명을 해놓았다. 내용을 참고하자면,

 데이터베이스 트랜잭션은 데이터베이스 관리 시스템 또는 유사한 시스템에서 상호작용의 단위이다.여기서 단위라는 말을 사용했는데, 쉽게 말하면 더 이상 쪼개질 수 없는 최소의 연산이라는 의미가 된다.

 예를 들어보자. 만약 내가 쇼핑 앱을 켜서 상품을 구매하려고 한다. 그런데 내가 결제를 하는 짧은 시간 사이에 아래와 같은 일이 벌어지면 어떨까?
 - 해당 판매자가 상품의 가격을 바꿔버려서, 잘못된 금액이 결제됨
 - 같은 상품을 다른 사람도 구매해서, 상품 재고는 1개인데 2명에게 결제됨
 - 결제가 완료되기 직전에 네트워크가 끊겨서, 돈은 나갔지만 구매완료는 되지 않음

 위의 상황이 일어나지 않기 위해선 아래의 조치가 필요할 것이다.
  - 내가 결제중일 때에는 해당 상품의 정보를 바꿀 수 없게 함
  - 내가 결제중일 때에는 해당 상품을 다른 사람이 결제하지 못하게 함
  - 내 구매가 오류로 완료되지 않았다면, 결제된 금액을 환불 처리함
 
 이를 정리하자면,
 "결제는 다른 사람과 독립적으로 이루어지며, 과정 중에 다른 연산이 끼어들 수 없다. 오류가 생긴 경우 연산을 취소하고 원래대로 되돌린다. 성공할 경우 결과를 반영한다."
 이며, 이를 DB특성인 ACID(원자성, 일관성, 고립성, 지속성)과 연관된다.


## @Transactional 어노테이션 활용예시

 @Transactional은 클래스나 메서드에 붙여줄 경우, 해당 범위 내 메서드가 트랜잭션이 되도록 보장해준다. 선언적 트랜잭션이라고도 하는데, 직접 객체를 만들 필요 없이 선언만으로도 관리를 용이하게 해주기 때문이다. 특히나 SpringBoot에서는 선언적 트랜잭션에 필요한 여러 설정이 이미 되어있는 탓에, 더 쉽게 사용할 수 있다. 
 
 예시로 아래 코드를 보자. 도서를 Repository를 통해 가져오는 서비스 레이어의 코드이다.
```java
    @RequiredArgsConstructor
    @Service
    public class BookService {
        private final BookRepository bookRepository;

        @Transactional(readOnly = true)
        public List<BookResponseServiceDto> getBooks() {
            return bookRepository.findAll()
                    .stream()
                    .map(BookResponseServiceDto::new)
                    .collect(Collectors.toList());
        }

        @Transactional(readOnly = true)
        public BookResponseServiceDto getBook(Long bookId) {
            final Book book = bookRepository.findById(bookId)
                    .orElseThrow(() -> new BookNotFoundException(bookId));

            return new BookResponseServiceDto(book);
        }
    }
```
 @Transactional 어노테이션을 getBooks()와 getBook() 메서드에 작성해 주었다. 그 결과로, 만약 getBook() 메서드가 실행될 경우 해당 메서드는 아래의 속성을 가진다.
 - 연산이 고립되어, 다른 연산과의 혼선으로 인해 잘못된 값을 가져오는 경우가 방지된다.
 - 연산의 원자성이 보장되어, 연산이 도중에 실패할 경우 변경사항이 Commit되지 않는다.
 - 위의 속성이 보장되기 때문에, 해당 메서드를 실행하는 도중 메서드 값을 수정/삭제하려는 시도가 들어와도 값의 - 신뢰성이 보장된다. 또한, 연산 도중 오류가 발생해도 rollback해서 DB에 해당 결과가 반영되지 않도록 할 수 있다


## @Transactional의 작동 원리와 흐름

 그렇다면 @Transactional이 붙은 메서드를 호출할 경우, Spring Framework에서는 어떤 일이 벌어질까?
 @Transactional이 클래스 내지 메서드게 붙을 때, Spring은 해당 메서드에 대한 프록시를 만든다. 프록시 패턴은 디자인 패턴 중 하나로, 어떤 코드를 감싸면서 추가적인 연산을 수행하도록 강제하는 방법이다. 트랜잭션의 경우, 트랜잭션의 시작과 연산 종료시의 커밋 과정이 필요하므로, 프록시를 생성해 해당 메서드의 앞뒤에 트랜잭션의 시작과 끝을 추가하는 것이다. 이러한 로직은 AOP에 바탕을 두고 설계되었기 때문에, 이후 설명에서 해당 프록시는 트랜잭션 AOP로 명칭하겠다.

 또한, 스프링 컨테이너는 트랜잭션 범위의 영속성 컨텍스트 전략을 기본으로 사용한다. 서비스 클래스에서 @Transactional을 사용할 경우, 해당 코드 내의 메서드를 호출할 때 영속성 컨텍스트가 생긴다는 뜻이다. 영속성 컨텍스트는 트랜잭션 AOP가 트랜잭션을 시작할 때 생겨나고, 메서드가 종료되어 트랜잭션 AOP가 트랜잭션을 커밋할 경우 영속성 컨텍스트가 flush되면서 해당 내용이 반영된다. 이후 영속성 컨텍스트 역시 종료되는 것이다.
 <figure>
 <img src="{{ "/media/img/Spring/transaction1.png" | absolute_url }}" />
 <figcaption>츨처: https://kafcamus.tistory.com/30 </figcaption>
 </figure>
 이러한 방식으로 영속성 컨텍스트를 관리해 주기 때문에, @Transactional을 쓸 경우 트랜잭션의 원칙을 정확히 지킬 수 있다. 또한, 아래의 원칙 역시 유의해야 한다. 만약 같은 트랜잭션 내에서 여러 EntityManager를 쓰더라도, 이는 같은 영속성 컨텍스트를 사용한다. 같은 EntityManager를 쓰더라도, 트랜잭션이 다르면 다른 영속성 컨텍스트를 사용한다.(아마, 다른 JDBC를 쓰더라도 트랜잭션단위로 묶인다는 의미인듯)


## @Transactional 옵션 정리

 @transactional 옵션에 대해서 https://devkingdom.tistory.com/287의 블로그에 적정한 예시와 사용 이유까지 친절히 설명되어 있어 해당 내용을 토대로 정리하였다.

 ### 1. __isolation__

    isolation 옵션을 트랜잭션에서 일관성없는 데이터를 어떻게 허용할지에 대한 허용 수준을 정할수 있는 옵션이다. 아래와 같은 형태로 설정을 해주면 된다.
    ```java
    @Service
    public class MemberService { 
        @Transactional(isolation=Isolation.DEFAULT) 
            public void addMember(MemberDto memberDto) throws Exception { 
            // 멤버 삽입 로직 구현 
        } 
    }
    ```
    __1) DEFAULT__ 

    기본적인 격리 수준이고, 기존 DB의 Isolation Level을 따르게 된다.

    __2) READ_UNCOMMITED (Level 0)__
    
    커밋되지 않는 데이터에 대한 읽기를 허용하는 방식이다.
    이렇게 설정하면 Dirty Read라는 문제가 발생할 수 있다.

    예를 들어 사용자 A가 1이라는 데이터를 2로 변경한다고 했을때, 사용자 B가 아직 완료되지 않은 (Uncommitted or Dirty) 데이터를 읽을 수 있는데, 만약 사용자가 A 가 수행한 데이터가 정상커밋되지 않아 롤백될 경우 사용자 B가 읽은 데이터는 잘못된 데이터가 되는 것이다.

    __3) READ_COMMITED (Level 1)__

    커밋된 데이터에 대해 읽기 를 허용하는 방식이다. 즉, 사특정 사용자가 데이터를 변경하는 동안 다른 사용자는 해당 데이터에 접근이 불가하다.
    이렇게 하면 Dirty Read 문제는 방지할 수 있으나, Non-Repeatable Read 문제가 생긴다.

    예를들어 사용자 A 가 1번 데이터를 조회하고 있을 때 사용자 B가 1번 데이터를 수정하고 커밋을 해버리면, 사용자 A가 같은 트랜잭션에서 다시 1번 데이터를 조회한다면 수정된 데이터가 조회되어 버려서 반복해서 데이터를 읽을 수 없는 경우를 의미한다.

    __4) REPEATABLE_READ (Level 2)__

    동일 필드에 대해 다중으로 접근할 때 동일한 결과를 보장하는 방식을 의미한다. 즉, 트랜잭션이 완료될 때까지 SELECT 문장이 사용되는 모든 데이터들에 대해 Shared Lock이 걸려 다른 사용자가 그 데이터에 대한 접근이 불가능해진다.
    그러므로 어떤 트랜잭션이 수행될 때 다른 트랜잭션이 앞선 트랜잭션이 사용 중인 데이터에 대해 갱신하거나 삭제하는게 불가능하기에 트랜잭션 내에서 여러번 데이터를 접근한다해도 데이터의 일관성을 보장할 수 있다.

    이렇게 하면 Non-Repeatable Read 문제는 방지할 수 있으나, Phantom Read 문제가 생긴다.

    예를들어 사용자 A 가 특정 범위의 데이터 [1,2,3,4] 를 2번 읽는 트랜잭션이 수행된다고 가정하자. 두번째 읽기전에 사용자 B가 수행한 트랜잭션이 [5,6,7,8] 이라는 데이터를 추가해 버리면 첫번째 쿼리와 두번째 쿼리의 결과가 달라진다.

    즉, 한 트랜잭션에서 일정한 범위의 레코드를 두번 이상 읽을 때, 데이터가 불일치하는 문제가 바로 Phantom Read 문제이다.

    __5) SERIALIZABLE (Level 3)__

    해당 설정은 데이터의 일관성과 동시성을 유지하기 위해 MVCC(Multi Version Concurrency Control) 을 사용 하지 않는다. (MVCC 는 다중 사용자 데이터 베이스 성능 을 위한 기술로 데이터를 조회할때 LOCK 을사용하지 않고 데이터의 버전을 관리하여 데이터 일관성과 동시성을 높이는 기술을 의미)
    그리고 트랜잭션이 완료될 때 까지 SELECT 문장이 사용하는 모든 데이터에 Shared Lock 을 걸어 다른 사용자가 해당 영역에 있는 모든 데이터에 대한 수정과 입력이 불가능하게 만들어 Phantom Read를 방지한다.

    설명만 보기에는 SERIALIZABLE 속성을 써야하겠지만 이렇게 격리수준이 높아지면 성능저하의 우려가 있으니 상황에 따라 적절한 속성을 사용해줘야한다.


 ### 2. __propagation__

    propagation 옵션은 트랜잭션이 동작할 때 다른 트랜잭션이 호출되면 어떻게 처리할지를 정하는 옵션이다.
    즉 피호출 트랜잭션에서 호출한 트랜잭션을 그대로 사용할지 새로 생성할지를 정하는 옵션이라고 생각하면된다.
    설정방법은 아래와 같이 적용해주면 된다.
    ```java
    @Service
    public class MemberService { 
        @Transactional(propagation=Propagation.REQUIRED)
        public void addMember(MemberDto memberDto) throws Exception { 
            // 멤버 삽입 로직 구현 
        } 
    }
    ```
    __1) REQUIRED__

    디폴트인 속성이고, 부모 트랜잭션 내에서 실행하게 하고 만약 부모 트랜잭션이 없으면 새로운 트랜잭션을 생성하게 하는 설정이다.

    __2) SUPPORTS__

    이미 시작된 트랜잭션이 있으면 그 트랜잭션에 참여하여 처리하게 하고, 없으면 트랜잭션없이 진행하게 하는 설정이다.

    __3) REQUIRES_NEW__

    부모 트랜잭션이 있어도 그냥 새롭게 트랜잭션을 생성하게 하는 설정이다.

    __4) MANDATORY__

    REQUIRED 처럼 이미 시작한 트랜잭션에 참여하지만 , 없으면 새로 생성하는게 아니라 예외를 발생시킨다. 보통 독립적으로 트랜잭션이 진행되면 안되는 경우에 해당 옵션을 사용한다.

    __5) REQUIRES_NEW__

    항상 새로운 트랜잭션으로 작업을 수행한다.. 만악에 진행중인 트랜잭션이 있으면 트랜잭션을 잠시 보류시킨다.

    __6) NOT_SUPPORTED__

    트랜잭션 없이 작업을 수행한다. 만약 진행중인 트랜잭션이 있으면 트랜잭션을 잠시 보류시킨다.

    __7) NEVER__

    트랜잭션을 사용하지 않게 강제한다. 이미 진행중인 트랜잭션이 있다면 Exception을 발생시키고 트랜잭션이 진행중이지 않을때 작업을 수행한다.

    __8) NESTED__

    이미 진행중인 트랜잭션이 있으면 중첩된 트랜잭션을 실행하고 없으며 REQUIRED와 동일하게 새로운 트랜잭션을 생성하여 진행한다. 중첩 트랜잭션을 말 그대로 트랜잭션안에 트랜잭션을 만드는 것이다. 트랜잭션안의 트랜잭션이 커밋되거나 롤백되어도, 바깥의 트랜잭션에게 영향을 주지 않는다.


 ### 3. __noRollbackFor, .rollbackFor__

    특정 예외 발생 시, rollback을 하거나 하지않게 하는 옵션이다.
    사용방법은 아래와 같다.
    ```java
    @Service
    public class MemberService { 
        @Transactional(noRollvackFor=CustomException.class) 
        public void addMember(MemberDto memberDto) throws Exception { 
            // 멤버 삽입 로직 구현 
        }
    }
    ```
    해당 속성을 보기전에 우선 Exception에 대해 조금 알 필요가 있겠다.
    https://devkingdom.tistory.com/283

    선언적 트랜잭션에서는 런타임 예외가 발생하면 롤백을 하도록한다. 반면 체크 익셉션이 발생하면 커밋을 한다. 스프링에서는 Data Acces 기술의 예외가 런터임 예외로 전환되어 던져지기 때문에 런타임 예외만 롤백대상이 되지만 기본동작을 바꿀수 가 있는데 그 옵션이 바로 rollbackFor 속성이다.

    그리고 반대로 특정 예외 발생시 Rollback 처리되지 않게 하는 옵션이 noRollbackFor 속성이다.


 ### 4. __timeout__
    지정한 시간 내에 메서드 수행이 완료되지 않으면 rollback 하게 하는 옵션이다. -1 이 default 값이고 이는 no timeout을 의미한다.
    사용 방법은 아래와 같다.
    ```java
    @Service
    public class MemberService { 
        @Transactional(timeout=10)
        public void addMember(MemberDto memberDto) throws Exception { 
            // 멤버 삽입 로직 구현 
        } 
    }
    ```
 

 ### 5. __readOnly__
    트랜잭션을 읽기전용으로 설정하는 옵션이다. true로 설정하면 insert, update, delete 실행할 때 예외가 발생한다.

    default 값은 false이다.
    사용방법은 아래와 같다.
    ```java
    @Service
    public class MemberService {
        @Transactional(readOnly =true)
        public List<Member> findAllMember() throws Exception { 
            // 전체 멤버 검색 로직 구현 
        } 
    }
    ```
    보통 get 이나 find 같은 이름의 메서드 앞에 이런식으로 설정이 되어있다. 성능을 최적화 하기위해서도 사용할 수 있다고하지만 나는 보통 특정 트랜잭션 작업에서 누군가 쓰기 작업을 하는 걸 의도적으로 방지하기 위해 사용해왔다.


## 참고 자료 및 사이트
 transaction 정의 정리
 - https://kafcamus.tistory.com/30
 transaction anotation 옵션 정리
 - https://devkingdom.tistory.com/287 
