## 다양한 연관관계 매핑

- 연관관계 매핑 시 고려사항 3가지: 다중성, 단방향/양방향, 연관관계의 주인

### 다대일 [N:1]

#### **다대일 단방향**

![image](https://user-images.githubusercontent.com/57944099/168225845-a3a9f952-399f-4c6f-a385-50de0fa4f694.png)

- 가장 많이 사용하는 형태, 반대는 1:N

#### **다대일 양방향**

![image](https://user-images.githubusercontent.com/57944099/168226280-310d7840-eadf-4f6a-8b78-da2dba58272d.png)

- 양쪽을 서로 참조하도록 개발

### 일대다 [1:N]

#### **일대다 단방향**

![image](https://user-images.githubusercontent.com/57944099/168227148-7fc671e3-6ff4-4731-aa40-31a9e7c0dcf4.png)

- 객체의 1:N 관계에서 1이 연관관계의 주인
- 테이블의 1:N 관계에서는 N쪽에 외래키 존재

#### **일대다 양방향**

![image](https://user-images.githubusercontent.com/57944099/168228471-e97bb2ef-58f3-41e5-b757-04f07b87fa1c.png)

- 공식적으로 존재하지 않는 매핑 방식, 읽기 전용 필드로 야매스럽게 구현은 할 수 있음
- `@JoinColumn(name="TEAM_ID, insertable=false, updatable=false)`
- 결론: **다대일 양방향을 사용하라**

### 일대일 [1:1]

- 반대도 일대일
- 주 테이블 or 대상 테이블 중 외래키 선택 가능
- 외래키에 unique 제약 조건 추가하기

#### **일대일: 주 테이블에 외래키 단방향**

![image](https://user-images.githubusercontent.com/57944099/168229684-58fab3bc-42c7-4a6a-b8af-a319a4084603.png)

- 다대일(N:1) 단방향 매핑과 유사

#### **일대일: 주 테이블에 외래키 양방향**

![image](https://user-images.githubusercontent.com/57944099/168229995-5d1080bc-b0f9-4195-9b4d-b84236e42ee6.png)

- 다대일 양방향 매핑 처럼 외래 키가 있는 곳이 연관관계의 주인, 반대편은 mappedBy 적용

#### **일대일: 대상 테이블에 외래키 단방향**

![image](https://user-images.githubusercontent.com/57944099/168230787-07243864-ba99-4499-a632-8f1204166738.png)

- 단방향 관계는 지원되지 않고, 양방향 관계는 지원

#### **일대일: 대상 테이블에 외래키 양방향**

![image](https://user-images.githubusercontent.com/57944099/168231097-508b8bbe-1e90-44d3-b6f9-e435ad188923.png)

### 다대다 [N:M]

![image](https://user-images.githubusercontent.com/57944099/168233283-eebebfdd-52c4-4197-be05-9b654004f721.png)

- 관계형 DB는 테이블 2개로 다대다 관계 표현 불가 -> 연결 테이블을 추가해서 일대다, 다대일 관계로 풀어내야
- 객체는 객체 2개로 다대다 구현 가능 (컬렉션 활용)

#### **다대다 구현하기**

- `@ManyToMany`
- `@JoinTable`로 연결 테이블 지정

#### **다대다 매핑의 한계**

- **편리해 보이지만 실무에서 사용X**
- 연결 테이블에 매핑 정보만 들어가고 다른 추가 정보를 넣을 수 없다는 단점

#### **다대다 한계 극복**

![image](https://user-images.githubusercontent.com/57944099/168233484-1e5b3a51-d9be-438b-baf4-bffe52befb6d.png)

- 연결 테이블용 엔티티 추가 - 연결 테이블을 엔티티로 승격
- `@ManyToMany` -> `@OneToMany` + `@ManyToOne`

-------------------

## 고급 매핑

### 상속관계 매핑

- 객체는 상속 관계가 있지만 관계형 DB는 없다
- 상속 관계 매핑 세가지 전략: 조인 전략, 단일 테이블 전략, 구현 클래스마다 테이블
- 주요 어노테이션
  - `@Inheritance(strategy=InheritanceType.XXX)`
    - `JOINED`, `SINGLE_TABLE`, `TABLE_PER_CLASS`
  - `@DiscriminatorColumn(name="DTYPE")`
  - `@DiscriminatorValue("XXX")`

#### **조인 전략**

![image](https://user-images.githubusercontent.com/57944099/168408328-c3c23ba9-d1be-441e-80c9-1eb5fda347b9.png)

- `@Inheritance(strategy=InheritanceType.JOINED)`

#### **단일 테이블 전략**

![image](https://user-images.githubusercontent.com/57944099/168411342-2af7da91-b09e-408a-b2e8-321f2965b804.png)

- 하나의 테이블에 다 관리하는 형식 (`DTYPE` 필수)
- `@Inheritance(strategy=InheritanceType.SINGLE_TABLE)`

#### **구현 클래스마다 테이블 전략**

![image](https://user-images.githubusercontent.com/57944099/168411458-889fff90-bc5d-4ddb-b6fc-ef6c9fda4624.png)

- 별개의 구체 타입만 관리하는 형식
- `@Inheritance(strategy=InheritanceType.TABLE_PER_CLASS)`
- 결론: 쓰지 마라

#### **정리하자면,**

- 조인 전략 or 단일 테이블 전략 둘 중 하나를 써라
- 각각의 trade-off를 고려해서 DBA와 상의해서 결정할 것

### Mapped Superclass - 매핑 정보 상속

- 공통 매핑 정보가 필요할 때 사용 (일일이 넣기 귀찮을 때 사용하기 좋음)

ex)

```java
private String createdBy;
private LocalDateTime createdDate;
private String lastModifiedBy;
private LocalDateTime lastModifiedDate;
```

- 공통 속성 정보를 담은 클래스 -> `@MappedSuperclass`

#### **특징**

- 상속관계 매핑이 아니다
- 엔티티 클래스가 아니고 테이블과 매핑되지 않음, 매핑 정보만 제공
- 추상 클래스(abstract) 권장
- 주로 전체 엔티티에서 공통으로 적용하는 정보를 모을 때 사용
