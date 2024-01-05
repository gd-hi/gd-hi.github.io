---
# Header
title: "스프링 부트(Spring Boot) JPA 공부"
excerpt: "JPA에 대해 알아본다"
name: gdhi
writer: gdhi
categories: [Spring Boot, 개념] # [메인 카테고리, 서브 카테고리]
tags:
  - [Spring Boot, 스프링 부트, JPA]

toc: true
toc_sticky: true

date: 2024-01-05
last_modified_at: 2024-01-05

# --- 아래 부터 content
---

![](https://velog.velcdn.com/images/gdhi/post/62ec71c3-0e68-4c93-92c4-b97c650b8eea/image.png)



# ❗Thymeleaf를 이용해 쇼핑몰 포트폴리오 작성 할 예정
> [책 정리 velog](https://velog.io/@codren/Spring-Boot-%EC%87%BC%ED%95%91%EB%AA%B0-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95) 

<br><br><br><br>

---

<br><br><br>

# 📖스프링 부트의 특징
> 1. 웹 어플리케이션 서버(내장)
2. 스프링 부트에서 스타터 의존성 제공
3. `XML` 설정 없이 설정 가능
4. 애플리케이션의 모니터링 관리가 용이

<br>

## 📌빌드 도구(쇼핑몰)
> - `Bulid Gradle` : 전 까지 사용했다
- `pom.xml(maven)` : 이번엔 이 녀석을 쓴다. 둘 다 각각의 장단점이 있다(배포 등).

<br><br><br><br>

---

<br><br><br>

# 📖프로젝트 만들기 (Maven)
> `Thymeleaf` 쇼핑몰 하기 전에 구조 확인을 위한 테스트 프로젝트

![](https://velog.velcdn.com/images/gdhi/post/39bf521c-69d1-4786-a2be-2c3e5a203ac7/image.png)

![](https://velog.velcdn.com/images/gdhi/post/f5a6da6a-c507-430f-b76c-2a079d1fbf77/image.png)

![](https://velog.velcdn.com/images/gdhi/post/6a572de6-007c-4d15-9413-3fb81a13d938/image.png)

![](https://velog.velcdn.com/images/gdhi/post/4b9b0a90-4b05-4cde-96f1-865545e730b7/image.png)
👉 `java` : 자바, `resources` : `HTML/CSS/JAVASCRIPT`

![](https://velog.velcdn.com/images/gdhi/post/e9617abf-c71f-46a7-87bf-d5a56e73e27c/image.png)

👉 테스트를 위한 폴더, 이번 부터 테스트를 많이 할 예정이다.

<br>

#### pom.xml

```html
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.2.0</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.springDemo</groupId>
	<artifactId>springDemo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>springDemo</name>
	<description>Strarting Spring Boot Project</description>
	<properties>
		<java.version>17</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```

👉 `dependency` 에 스프링 웹, 테스트가 잘 들어가 있다. `Bulid Gradle` 과 유사하며 크롤링 할 때 이미 본 거라 따로 알아야 할 것은 없다

<br>

#### application.properties
```java
server.port = 80
application.name = springDemo

```

👉 `localhost:8081` 이 아닌 `80`은 `root`라 바로 접속이 된다.

<br><br>

## 📌테스트하기

<br>

### 📍SpringDemoApplication
```java
package com.springDemo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController // @Controller + @ResponseBody
@SpringBootApplication
public class SpringDemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringDemoApplication.class, args);
	}
	@GetMapping(value = "/")
	public String HelloWorld(){
		return "Hello World";
	}

}

```
👉 `@RestController` 에 `@ResponseBody` 가 있다. ❗테스트 용이라 메인에 쓴 거지 이렇게 사용하면 안된다

![](https://velog.velcdn.com/images/gdhi/post/a0376a1c-4fb7-45ff-a77c-d397c10dcbaa/image.png)

<br><br>

## 📌⚡롬복 추가하기⚡
>  **`@`, `어노테이션`, `애노테이션` 복습하기**
>
> `메타데이터`라 불리며
클래스나 메소드, 변수 등을 선언 할 때 `@`를 붙여서 사용한다. `어노테이션`은 컴파일러에게 `정보`를 알려주거나, `실행`할 때. `별도의 처리`가 필요할 때 등 매우 `다양한 용도`로 사용 가능하다.
>
> **롬복의 `어노테이션` 들**
- `@Getter/setter` : get과 set 
- `@ToString` : 객체 출력시 정의 한 멤버변수로 출력 👉 객체 오버라이딩
- `@ToString(exclude={"변수명"})` : 원하지 않는 변수 제외하고 출력 
- `@NonNull` : 해당 변수 `null` 체크, `null` 이면 에러 
- `@EqualsAndHashCode` : Equalse(), hashCode() 자동 생성
- `@Bulider` : 빌더 패턴 사용
- `@NoArgsConstructor` : 디폴트 생성자 추가 
- `@AllArgsConstructor` : 모든 속성에 대한 생성자 추가
- ⚡`@RequiredArgsConstructor` : 초기화되지 않은 `Final`, `@NonNull` `어노테이션`이 붙은 필드에 대한 생성자 생성 👉`@Autowried` 필요 없이 주입이 가능하게 한다.⚡
- `@Log` : 로그 변수 자동 생성
- `@Value` : 불변 클래스 생성
- ⚡`@Data` : 모두 다⚡


[Project Lombok Maven](https://mvnrepository.com/artifact/org.projectlombok/lombok/1.18.30)
![](https://velog.velcdn.com/images/gdhi/post/080e838e-9dab-4eb6-93e0-fa2a60db3430/image.png)

👉 버전은 지워 준다. 자동으로 설정해줌

![](https://velog.velcdn.com/images/gdhi/post/47311e8e-2bd6-47f4-84e5-bd2c1e0a3d87/image.png)

👉 늘 하던대로 추가하기

![](https://velog.velcdn.com/images/gdhi/post/e6d43f8b-cdd7-46c4-aaf3-0d89a7756f01/image.png)

<br>

### 📍예제 테스트

<br>

#### UserDto 클래스
```java
package com.springDemo;

import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

@Getter
@Setter
@ToString
// @Data
public class UserDto {
    private String name;
    private int age;
}


```

<br><br>

#### TestController 클래스
```java
package com.springDemo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestController {
    @GetMapping(value = "/test")
    public UserDto test(){ // ⚡객체 리턴⚡
        UserDto userDto = new UserDto();
        userDto.setAge(20);
        userDto.setName("홍길동");
        return userDto;
    }
}

```

<br><br>

### 📍결과
![](https://velog.velcdn.com/images/gdhi/post/dc040bca-3a9a-4b2e-87f4-26cec35525ab/image.png)
👉 `key : 값`, `json` 형태

<br><br>

## 📌MySQL 설정
> 이미 많이 했으므로 스킵

<br><br><br><br>

---

<br><br><br>

# 📖⭐[JPA](https://velog.io/@adam2/JPA%EB%8A%94-%EB%8F%84%EB%8D%B0%EC%B2%B4-%EB%AD%98%EA%B9%8C-orm-%EC%98%81%EC%86%8D%EC%84%B1-hibernate-spring-data-jpa)⭐
> Java Persistence API
자바 `ORM` 기술에 대한 `API` 표준, 스프링에서 제공하는 것이 아님
- `ORM` : Object Relational Mapping, 객체 관계(관계 데이터베이스) 연결(맵핑). 즉 객체와 데이터베이스를 연결
![](https://velog.velcdn.com/images/gdhi/post/9cf57f27-239f-4ae9-b3c3-83179baa1947/image.png)
👉 `MyBatis`는 끝났다
>> `MySQL` - 관계형 데이터 베이스
`Java` - 객체 지향 언어
👉 관계와 객체는 패러다임이 불일치!!
👉 `ORM` 으로 연결 할 수 있다
👉 `ORM` 가지고 표준 `API`를 만든게 `JPA`
👉 `JPA`는 인터페이스(이 기종간의 연결), 대표적인 구현체, `Hibernate`, `EclipseLink`, `DataNucleus`, `OpenJpa`, `TopLink`...
👉 우리는 `Hibernate`를 사용한다
>
간단한 메소드로 `CRUD` - `INSERT`, `UPDATE`, `SELECT`, `DELETE` 가 가능
>
👉 **우리가 만든 객체로 테이블이 자동 생성!!**
>
👉 먼소리야??? 나중가면 이해하게 되있다.

<br><br>

## 📌JPA 장점
>**1. sql 중심적인 개발에서 객체 중심적인 개발이 가능하다.**
sql 코드의 반복, 객체지향과 관계지향 데이터베이스의 `페러다임 불일치`
`Object -> [SQL 변환] -> RDB에 저장`
`[개발자 == SQL 매퍼]` 라고 할만큼 SQL 작업을 너무 많이 하고 있다.
>
**2. 생산성이 증가**
간단한 메소드로 `CRUD`가 가능하다
>
**3. 유지보수가 쉽다**
`기존` : 필드 변경 시 모든 SQL을 수정해야 한다.
`JPA` : 필드만 추가하면 된다. `SQL`은 `JPA`가 처리하기 때문에 손댈 것이 없다.
>
4. `Object`와 `RDB` 간의 `패러다임 불일치 해결`

<br><br>

## 📌JPA 단점
> 1. 복잡한 쿼리 처리 (`JPQL` 같은 건 자바를 고통스럽게 한다..)
2. 성능 저하. 자동으로 만들어 지니까 무겁다
👉 데이터베이스를 공부해야한다..


<br><br>

## 📌JPA 동작 방식
> ![](https://velog.velcdn.com/images/gdhi/post/c4916c79-3dc4-474d-a778-728e150ed509/image.png)


<br>

### 📍Entity
> 데이터베이스의 테이블에 대응하는 클래스
```java
item.java 👉 @Entity
				class item
```
👉 `@Entity` 어노테이션 붙은 클래스를 `JPA`가 관리

<br>

### 📍Entity Manager Factory
> 엔티티 매니저 인스턴스를 관리하는 주체로 한개만 만들어 진다. 사용자로부터 요청이 오면 엔티티 매니저를 생성

<br>

### 📍Entity Manager
> `Persistence Context` 에 접근하여 DB 작업을 제공하는 객체. 내부적으로 `DB Connection` 을 이용해서 `DB` 에 접근
>
**기본 제공 메소드**
- `Find()` : 검색
- `persist()` : 저장
- `remove()` : 삭제 
- `flush()` : 반영

<br>

### 📍Persistence Context
> Entity 를 영구 저장하도록 지원하는 환경, 엔티티 매니저를 통해 접근 가능

<br>

## 📌Entity Life Cycle(엔티티 생명주기)
>![](https://velog.velcdn.com/images/gdhi/post/65a94463-8755-467b-b321-80dd9a132612/image.png)
`비영속 (new)`
- `new` 키워드를 통해 생성된 상태로 아직 영속성 컨텍스트에 저장되지 않음
>
`영속 (managed)`
- 엔티티가 영속성 컨텍스트에 저장되어 관리되는 상태
- 아직 DB 에 저장된 상태 ❌, 트랜잭션 `Commit` 후에 `DB` 에 반영
>
`준영속 상태 (detached)`
- 영속성 컨텍스트에 엔티티가 저장되었다가 분리된 상태
>
`삭제 상태 (removed)`
- 영속성 컨텍스트와 데이터베이스에서 삭제된 상태

<br>

### 📍예제 코드
> 
```java
Item item = new Item();		// ①
item.setItemNm("테스트 상품");	
EntityManager em = entityManagerFactory.createEntityManager();	// ②
EntityTransaction transaction = em.getTransaction();		// ③
transaction.begin();		
em.persist(item);		// ④
transaction.commit();		// ⑤
em.close();			// ⑥
```     
① new 키워드로 영속성 컨텍스트에 담을 상품 `엔티티 생성`(아직 안에 담기지 않음)
② 엔티티 매니저 팩토리로부터 `엔티티 매니저를 생성`
③ 데이터 변경 시 무결성을 위해 `트랜잭션 시작`
④ 영속성 컨텍스트에 저장된 상태, 아직 DB에 `INSERT SQL 보내기 전`
⑤ 트랜잭션을 DB에 반영, 이 때 실제로 `INSERT SQL 수행`
⑥ 엔티티 매니저와 엔티티 매니저 팩토리 자원을 `close() 호출로 반환`

<br><br>

## 📌Persistence Context 사용 시 이점
> 애플리케이션과 데이터베이스 사이에 영속성 컨텍스트 `중간 계층` 버퍼링, 캐싱 등을 할 수 있는 장점이 있다. 속도가 빨라진다는 것

<br>

### 📍1. 1차 캐시
>![](https://velog.velcdn.com/images/gdhi/post/bc97707d-bb93-499b-9ac2-6c516ed8480d/image.png)
영속성 컨텍스트 내부에는 위와 같이 `1차 캐시`라는 공간이 있다.
`persist()`를 한다고 해서 바로 `DB`에 `insert`문이 날아가는게 아닌, 해당 1차 캐시 공간에 저장된다.
이렇게 된다면, 특정 `Entity`에 대한 삽입과 조회가 동시에 일어나게 된다면 `DB`에 `insert`문와 `select`문을 실행해 `DB`에 접근할 필요 없이 단순히 이 `1차 캐시`에서 `Entity`를 가져오기만 하면 되는 것이다.
![](https://velog.velcdn.com/images/gdhi/post/5e94b1c5-5f2b-4c14-949e-35e8ca0fad1f/image.png)
단, `1차 캐시`는 한 `Transaction`의 생명주기가 같으므로 눈에 띄는 큰 속도 이점은 없다고 볼 수 있다.

<br>

### 📍2. 동일성 보장
>`DB`에서 조회한 `Data`를 기반으로 새로운 `Entity`를 생성하는 것이 아닌, 1차 캐시에서 삽입, 조회를 하므로, `JCF`같이 동일성 보장이 된다. 하나의 트랜잭션에서 같은 키값으로 같은 엔티티 조회를 보장받는다
```java
   User findUser1 = em.find(User.class, "user1");
   User findUser2 = em.find(User.class, "user2");
   System.out.println(findUser1 == findUser2); // true
```
👉 같은 객체를 바라보고 있다

<br>

### 📍3. 쓰기 지연
>`Entity`들을 `1차 캐시`에 저장할 때, 저장할 뿐만 아니라 쓰기 지연 `SQL 저장소`라는 곳에 해당 `SQL문`도 함께 저장한다
![](https://velog.velcdn.com/images/gdhi/post/ad25802f-0bb3-4006-9659-d6e549ed0f5b/image.png)
미리 `SQL문`들을 저장하고 있다가, `트랜잭션 커밋`을 실행할 때, 미리 저장해두었던 SQL문들을 `한번에 실행`한다.
즉, 버퍼링같은 기능. `Commit` 호출 시 쌓인 `SQL문`들이 `flush()` 되면서 `DB` 에 반영 👉 성능 이점

<br>

### 📍4. 변경 감지
>`jpa`에서 `update`를 할 때, 따로 `update()`나 `modify()`가 없다
```java
    Member member = entityManager.find(Member.class, 1l)
    member.setName("updateName");
```
`find()`로 `member`를 조회한 다음 `data`를 변경하기만 하면 이를 감지해, `commit()`할 때 `update문이 알아서 생성`되어 날아간다.
이는 아래와 같은 흐름덕분에 가능하다
![](https://velog.velcdn.com/images/gdhi/post/92c53c40-428c-40c3-92c2-d25d5006551b/image.png)
1차 캐시에는 스냅샷이라는 공간이 있다.
- `snapshot` : 1차(최초)로 객체가 들어왔을 시점을 저장
- `Entity` : 실제 저장된 `Entity`
`flush()`가 실행될 때, 먼저 `Entity`와 스냅샷을 비교한다.
위의 코드에서는 `Name`을 변경했기에, `Entity`의 `Name`과 스냅샷의 `Name이 다른 것`을 감지해 자동으로 `Update문을 생성`하는 것이다.


<br><br><br><br>

---

<br><br><br>

# 💷쇼핑몰 프로젝트
> **이걸로 쭈---욱 만든다**

![](https://velog.velcdn.com/images/gdhi/post/092d07cc-8a21-4d72-be0c-977ac665ca75/image.png)

![](https://velog.velcdn.com/images/gdhi/post/e9464118-4464-4eb2-b20d-f30dcead39e4/image.png)
`Thymeleaf` : 서버에서 가공한 데이터를 뷰에 보여주기 위한 템플릿(`.html`)
`H2 Database` : 자바 기반의 관계형 데이터베이스로 매우 가볍고 빠른 데이터베이스 (`테스트용`)


![](https://velog.velcdn.com/images/gdhi/post/2ce352fd-0798-4aee-8f1e-38d6368d939f/image.png)

👉 안전한 파일로

![](https://velog.velcdn.com/images/gdhi/post/32380ebc-5107-4c31-be4e-f7fdb08db2eb/image.png)
👉 프로젝트 `open`
![](https://velog.velcdn.com/images/gdhi/post/de063f4b-b019-4723-b728-ae3ac7eaf383/image.png)

<br>

## 📌shop 스키마 생성
![](https://velog.velcdn.com/images/gdhi/post/21c8e6ec-4a7b-486e-a74b-f26203127f43/image.png)


<br><br>

## 📌⭐application.properties⭐
>
```java
server.port = 80 👉 포트 설정
>
spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver 👉 데이터베이스 설정
spring.datasource.url = jdbc:mysql://localhost:3306/shop 
spring.datasource.username = root 
spring.datasource.password = 1234 
>
spring.jpa.properties.hibernate.show_sql = true 👉 실행되는 쿼리를 출력
>
spring.jpa.properties.hibernate.format_sql = true 👉 쿼리의 가독성을 높이기 위해 포멧팅
>
logging.level.org.hibernate.type.descriptor.sql = trace 👉 쿼리에 물음표로 출력되는 바인드 파라미터 출력
>
spring.jpa.hibernate.ddl-auto = create 👉  DDL AUTO 옵션
spring.jpa.database-platform = org.hibernate.dialect.MySQL8Dialect 👉 MySQL 버전 8
>
```
>
**❗DDL AUTO 옵션**
- `None` : 사용하지 않음, 우리가 원래 하던 방식으로 사용해야 함
- `Create` : 기존테이블 삭제 후 테이블 생성
- `Create-drop` : 기존테이블 삭제 후 테이블 생성, 종료 시점에 테이블 삭제. 서버를 끄면 삭제 된다.
- `Updata` : 변경된 스키마 적용
- `Validate` : 엔티티와 테이블 정상 매핑 확인 (스테이징). 상용화 된 순간 `Validate`로 바꾼다. `Create`로 해놓으면 난리남

<br><br>

## 📌constant 패키지 생성
![](https://velog.velcdn.com/images/gdhi/post/b1f243c3-1ecb-4225-a1ff-6f150598388d/image.png)


<br><br>

### 📍ItemSellStatus enum 생성
> 상태는 전부 `enum`으로 관리, 상품의 현재 판매 상태 (판매, 품절)를 나타내는 상수값 `SELL`, `SOLD_OUT`

```java
package com.shop.constant;

public enum ItemSellStatus {
    SELL, SOLD_OUT // SELL - 0, SOLD_OUT - 1
}
```
<br><br>

## 📌entity 패키지 생성
![](https://velog.velcdn.com/images/gdhi/post/b5f390c0-b84c-49b3-9587-d8c2fd1a2ab3/image.png)


<br>

### 📍item 클래스
```java
package com.shop.entity;


import com.shop.constant.ItemSellStatus;
import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

import java.time.LocalDateTime;

@Getter
@Setter
@ToString
public class item {
    private Long id;                        // 상품 코드
    private String itemNm;                  // 상품 명
    private int price;                      // 가격
    private int stockNumber;                // 재고 수량
    private String itemDetail;              // 상품 상세 설명
    private ItemSellStatus itemSellStatus;  // 상품 판매 상태,  ItemSellStatus 열거형의 값을 가짐
    private LocalDateTime regTime;          // 등록 시간
    private LocalDateTime updateTime;       // 수정 시간
}

```

<br><br>

## 📌Entity 관련 Annotation
> - ⚡`@Entity` : 클래스를 엔티티로 선언⚡
- ⚡`@Table` : 엔티티와 매핑할 테이블을 지정. index 지정 시에도 사용 ⚡
- `@Id` : 테이블의 기본키에 사용할 `Primary Key` 지정
- `@GeneratedValue` : 키 값을 자동 증감하도록 지정
- `@Column` : 필드와 컬럼 매핑. 세부적으로 지정이 필요함
- `@Lob` : 큰 데이터를 받을 때 사용. `BLOB`, `CLOB` 타입 매핑, 긴문자열 지정
- `@CreationTimestamp` : `insert`시 시간 자동 저장(DB 반영 시점)
- `@UpdateTimestamp` : `update`시 시간 자동 저장(DB 반영 시점)
- `@Enumerated` : `enum` 타입 매핑
- `@Transient` : 해당 필드 데이터베이스로 매핑 무시. 보안상 중요 자료
- `@Temporal` : 날짜 타입 매핑
- `@CreatedDate` : 엔티티 생성 시점의 시간을 자동 저장
- `@LastModifiedDate` : 조회한 엔티티의 값을 변경하는 시점의 시간을 자동 저장

<br>

### 📍기본키 전략 4가지
>- ⚡`AUTO` : 자동 ⚡
- `IDENTITY` : 데이터베이스 위임 
- `SEQUENCE` : 시퀀스 오브젝트를 이용
- `TABLE` : 키 생성용 테이블 사용

<br>

### 📍@Column 추가 속성
> - `name` : Mapping할 Column의 이름을 지정
- `insertable` : `Entity` 저장 시 해당 `Field`도 저장,
`false`로 읽기 전용 설정 가능
- `updatable` : `Entity` 수정 시 해당 `Field`도 수정,
`false`로 읽기 전용 설정 가능
- `table` : 하나의 `Entity`설정에서 두 개이상 `Table`에 매핑할 때 사용
- `nullable(DDL)` : `true/false`로 `null` 허용 여부 설정
- `unique(DDL)` : `true/false`로 `Unique` 제약 조건 설정
- `length(DDL)` : `Column` 속성 길이 설정
- `columnDefinition(DDL)` : `DB Column` 정보를 직접 설정
- `precision, scale(DDL)` : `BigDecimal`, `BigInteger Type`에서 사용,
`precision`은 소수점 포함 전체 자릿수,
`scale`은 소수 자릿수,
`double`, `float Type`에는 적용되지 않음.
아주 큰 숫자나 정밀한 소수를 다룰때 사용


<br><br>

## 📌상품 Entity 적용하기

<br>

### 📍Item 클래스
```java
package com.shop.entity;


import com.shop.constant.ItemSellStatus;
import jakarta.persistence.*;
import lombok.Getter;
import lombok.Setter;
import lombok.ToString;

import java.time.LocalDateTime;

@Entity
@Table(name = "item")
@Getter
@Setter
@ToString
public class Item {
    @Id // 기본 키
    @Column(name = "item_id")
    @GeneratedValue(strategy = GenerationType.AUTO) // 자동으로 1씩 증가
    private Long id;                        // 상품 코드
    
    @Column(nullable = false, length = 50)  // not null, itemNm이 상품 명
    private String itemNm;                  // 상품 명
    
    @Column(name = "price", nullable = false) // not null
    private int price;                      // 가격

    @Column(nullable = false)               // not null, stockNumber이 상품 명
    private int stockNumber;                // 재고 수량
    
    @Lob
    @Column(nullable = false)
    private String itemDetail;              // 상품 상세 설명
    
    @Enumerated(EnumType.STRING)            // 타입이 0, 1 이 아니라 SELL, SOLD_OUT
    private ItemSellStatus itemSellStatus;  // 상품 판매 상태  ItemSellStatus 열거형의 값을 가짐
    
    private LocalDateTime regTime;          // 등록 시간
    
    private LocalDateTime updateTime;       // 수정 시간
}


```

<br><br>

### 📍결과
![](https://velog.velcdn.com/images/gdhi/post/e901a083-56b9-48c4-8b0d-015b19dc44ac/image.png)
👉 콘솔창, `ShopApplication` 실행 시 `@Entity` 지정된 클래스를 기반으로 테이블 매핑
`spring.jpa.hibernate.ddl-auto=create` 설정으로 인해서 `테이블 drop` 후 `create`
`Hibernate` 에서 자동으로 `bigint` 형식으로 `sequence++`

![](https://velog.velcdn.com/images/gdhi/post/a12d3b02-b840-4c89-8b8d-e3bd862a2de8/image.png)

👉 자동으로 들어갔다

<br><br>

## 📌Repository 설계 패키지 생성
> `Spring Data JPA` 를 이용해 

![](https://velog.velcdn.com/images/gdhi/post/d25ec9a8-ef4a-476c-b2c9-58b0ba54f3c8/image.png)


<br>

### 📍Item에 대한 쿼리문을 날리는 ItemRepository 인터페이스 생성
![](https://velog.velcdn.com/images/gdhi/post/db0577e1-adda-41f7-a97d-c5ae02278303/image.png)
👉 제네릭 구조인 `JpaRepository` 상속
```java
package com.shop.repository;

import org.hibernate.cache.spi.support.AbstractReadWriteAccess;
import org.springframework.data.jpa.repository.JpaRepository;

// Item에 대한 쿼리문을 날리는 인터페이스
public interface ItemRepository extends JpaRepository<AbstractReadWriteAccess.Item, Long> { // 인터페이스 상속은 인터페이스만
		
}

```

<br><br>

## 📌Test를 위한 application-test.properties
![](https://velog.velcdn.com/images/gdhi/post/bce32a2d-ec4b-479c-bdd7-99bf4dc59db2/image.png)
![](https://velog.velcdn.com/images/gdhi/post/8e485101-7e93-4d02-a38b-92d688282c2c/image.png)


```java
spring.datasource.driver-class-name = org.h2.Driver 👉 Datasource 설정
spring.datasource.url = jdbc:h2:mem:test
spring.datasource.username = sa
spring.datasource.password = 

spring.jpa.database-platform = org.hibernate.dialect.H2Dialect 👉 H2 데이터베이스 설정

```
<br><br>

## 📍ItemRepository 테스트 하기
> 진짜 쿼리문을 날릴까?

![](https://velog.velcdn.com/images/gdhi/post/8071f458-367a-4862-82fc-edf09529eaa5/image.png)

![](https://velog.velcdn.com/images/gdhi/post/2d32e38c-2ca7-49d1-b559-f9ce7a1a0e1b/image.png)

![](https://velog.velcdn.com/images/gdhi/post/89dc82ce-8b64-421b-8d6d-04b836a732ad/image.png)

<br>

[JUnit Test Code](https://velog.io/@roycewon/JUnit-101)

<br>


```java
package com.shop.repository;

import com.shop.constant.ItemSellStatus;
import com.shop.entity.Item;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.TestPropertySource;

import java.time.LocalDateTime;

import static org.junit.jupiter.api.Assertions.*;

// 실제 Application 을 구동할 때 처럼 모든 Bean을 IOC 컨테이너에 생성
@SpringBootTest
// 테스트 코드 실행 시 application.properties 와 겹치는 부분은 우선순위를 갖는다
@TestPropertySource(locations = "classpath:application-test.properties")
class ItemRepositoryTest {
    @Autowired // 자동와이어
    ItemRepository itemRepository;

    @Test // 테스트 메소드
    @DisplayName("상품 저장 테스트") // 테스트 이름 설정
    public void createItemTest(){
        Item item = new Item();
        item.setItemNm("테스트 상품");
        item.setPrice(10000);
        item.setItemDetail("테스트 상품 상세 설명");
        item.setItemSellStatus(ItemSellStatus.SELL);
        item.setStockNumber(100);
        item.setRegTime(LocalDateTime.now());
        item.setUpdateTime(LocalDateTime.now());
        // itemRepository 👉 save inset 실제 테이블
        // MySQL로 테스트? ❌ 👉 H2로 확인한다(경량화 되어 있어 빠르기 때문에 테스트에 용이)
        Item savedItem = itemRepository.save(item);
        // Object 클래스 상속 받아서 오버라이딩
        System.out.println(savedItem.toString());
    }
}
```

<br><br>


### 📍결과

```java
Hibernate: 
    drop table if exists item cascade 
Hibernate: 
    drop sequence if exists item_seq
Hibernate: 
    create sequence item_seq start with 1 increment by 50
Hibernate: 
	// 테이블 생성
    create table item (
        price integer not null,
        stock_number integer not null,
        item_id bigint not null,
        reg_time timestamp(6),
        update_time timestamp(6),
        item_nm varchar(50) not null,
        item_sell_status varchar(255) check (item_sell_status in ('SELL','SOLD_OUT')),
        item_detail clob not null,
        primary key (item_id)
    )
Hibernate: 
    select
        next value for item_seq
Hibernate: 
	// insert 하기
    insert 
    into
        item
        (item_detail, item_nm, item_sell_status, price, reg_time, stock_number, update_time, item_id) 
    values
        (?, ?, ?, ?, ?, ?, ?, ?)
// toString 결과
Item(id=1, itemNm=테스트 상품, price=10000, stockNumber=100, itemDetail=테스트 상품 상세 설명, 
itemSellStatus=SELL, regTime=2023-12-05T10:04:05.888743800, updateTime=2023-12-05T10:04:05.888743800)

```

>❗ `JpaRepository save()` 👉 JPA는 인터페이스만 작성하면 쿼리문 실행
   `런타임 시점`(프로그램 실행 시간(실행 중))에서 [Dynamic Proxy](https://velog.io/@dev_leewoooo/Dynamic-Proxy%EB%9E%80)(동적 프록시(중간 서버))를 이용해 객체를 동적으로 생성
           `DAO(Data Access Object)`, `xml`이 필요가 없다

