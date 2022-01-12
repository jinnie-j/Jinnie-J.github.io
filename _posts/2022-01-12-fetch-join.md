---
title:  "JPA 패치 조인"
excerpt: "일반 조인 vs 패치 조인"

categories:
  - JPA
tags:
  - [JPA]

toc: true
toc_sticky: true
 
date: 2022-01-12
last_modified_at: 2022-01-12
---

## 일반 조인과 패치 조인

### 엔티티 
- order
    ```java
    @Entity
    @Table(name = "orders")
    @Getter @Setter
    @NoArgsConstructor(access = AccessLevel.PROTECTED)
    public class Order {

        @Id @GeneratedValue
        @Column(name = " order_id")
        private Long id;

        @ManyToOne(fetch = LAZY)
        @JoinColumn(name = "member_id")
        private Member member;


        @OneToOne(fetch = LAZY , cascade = CascadeType.ALL)
        @JoinColumn(name = "delivery_id")
        private Delivery delivery;
    ```
- member
    ```java
    @Entity
    @Getter @Setter
    public class Member {

        @Id@GeneratedValue
        @Column(name = "member_id")
        private Long id;


        @JsonIgnore
        @OneToMany(mappedBy= "member")
        private List<Order> orders = new ArrayList<>();
    ```
- delivery
    ```java
    @Entity
    @Getter @Setter
    public class Delivery {

        @Id @GeneratedValue
        @Column(name = "delivery_id")
        private Long id;

        @JsonIgnore
        @OneToOne(mappedBy= "delivery", fetch = LAZY)
        private Order order;
    ```
- order 객체에서 member, delivery 객체로의 Fetch Strategy를 Lazy로 설정하였다.

### 일반 조인
- JPQL
    ```java
        public List<Order> findAllByString() {
         return em.createQuery(
                "select 0 from Order o "+
                        " join o.member m" +
                        " join o.delivery d", Order.class
        ).getResultList();
    }
    ```
- 실행 쿼리
    ```sql
    select order0_.order_id as order_id1_6_,
    order0_.delivery_id as delivery4_6_,
    order0_.member_id as member_i5_6_,
    order0_.order_date as order_da2_6_,
    order0_.status as status3_6_ 
    from orders order0_ 
    inner join member member1_ on order0_.member_id=member1_.member_id 
    inner join delivery delivery2_ on order0_.delivery_id=delivery2_.delivery_id 
    ```
- 객체를 모두 따로 조회하여 가져옴으로써 성능상 문제가 발생할 수 있다. -> N+1문제


### fetch join
- JPQL
    ```java
        public List<Order> findAllByString() {
         return em.createQuery(
                "select 0 from Order o "+
                        " join fetch o.member m" +
                        " join fetch o.delivery d", Order.class
        ).getResultList();
    }    
    ```
- 실행 쿼리
    ```sql
    select order0_.order_id as order_id1_6_0_,
    member1_.member_id as member_i1_4_1_,
    delivery2_.delivery_id as delivery1_2_2_,
    order0_.delivery_id as delivery4_6_0_,
    order0_.member_id as member_i5_6_0_,
    order0_.order_date as order_da2_6_0_,
    order0_.status as status3_6_0_,
    member1_.city as city2_4_1_,
    member1_.street as street3_4_1_,
    member1_.zipcode as zipcode4_4_1_,
    member1_.name as name5_4_1_,
    delivery2_.city as city2_2_2_,
    delivery2_.street as street3_2_2_,
    delivery2_.zipcode as zipcode4_2_2_,
    delivery2_.status as status5_2_2_
    from orders order0_ inner join member member1_ on order0_.member_id=member1_.member_id 
    inner join delivery delivery2_ on order0_.delivery_id=delivery2_.delivery_id
    ```
- 일반 조인에서는 단순히 member, delivery의 식별자 값만 가지고 있었다면, 패치 조인에서는 member, delivery 원본 객체를 가져왔다.


### 정리
- 일반 조인
  - 연관 엔티티에 join을 하게되면 Select 대상의 엔티티는 영속화하여 가져오지만, 조인의 대상은 영속화하여 가져오지 않는다.
  - 연관 엔티티가 검색 조건에 포함되고, 조회의 주체가 검색 엔티티뿐일 때 사용하면 좋다.
  
- 패치 조인
  - 연관 엔티티에 fetch join을 하게되면 select 대상의 엔티티뿐만 아니라 조인의 대상까지 영속화하여 가져온다.
  - 연관 엔티티까지 select의 대상일 때, N+1의 문제를 해결하여 가져올 수 있는 좋은 방법이다.

- 쿼리 방식 선택 권장 순서
    - 우선 엔티티를 DTO로 변환하는 방법을 선택한다.
    - 필요하면 패치 조인으로 성능을 최적화 한다. -> 대부분의 성능 이슈가 향상된다.
    - 그래도 안되면 DTO로 직접 조회하는 방법을 사용한다.
    - 최후의 방법은 JPA가 제공하는 네이티브 SQL이나 스프링 JDBC Template을 사용해서 SQL을 직접 사용한다.