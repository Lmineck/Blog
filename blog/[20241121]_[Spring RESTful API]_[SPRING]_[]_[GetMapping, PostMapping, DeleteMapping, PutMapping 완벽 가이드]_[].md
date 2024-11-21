# Spring RESTful API: `@GetMapping`, `@PostMapping`, `@DeleteMapping`, `@PutMapping` 완벽 가이드

Spring Framework는 RESTful API를 구현하는 데 있어 간결하고 직관적인 방법을 제공합니다. 이 글에서는 REST API의 핵심인 `@GetMapping`, `@PostMapping`, `@DeleteMapping`, `@PutMapping` 어노테이션을 중심으로 사용법과 특징을 자세히 살펴보겠습니다.

---

## 목차

1. [Spring에서 RESTful API란?](#spring에서-restful-api란)
2. [`@GetMapping`: 데이터 조회를 위한 어노테이션](#getmapping-데이터-조회를-위한-어노테이션)
3. [`@PostMapping`: 새로운 데이터 생성을 위한 어노테이션](#postmapping-새로운-데이터-생성을-위한-어노테이션)
4. [`@PutMapping`: 데이터 수정을 위한 어노테이션](#putmapping-데이터-수정을-위한-어노테이션)
5. [`@DeleteMapping`: 데이터 삭제를 위한 어노테이션](#deletemapping-데이터-삭제를-위한-어노테이션)
6. [HTTP 메서드별 어노테이션 비교](#http-메서드별-어노테이션-비교)
7. [결론](#결론)

---

## Spring에서 RESTful API란?

RESTful API는 웹 애플리케이션의 자원을 HTTP를 통해 다룰 수 있게 하는 아키텍처 스타일입니다. Spring에서는 `@RestController`와 다양한 HTTP 메서드 어노테이션을 통해 RESTful API를 간단히 구현할 수 있습니다.

---

## `@GetMapping`: 데이터 조회를 위한 어노테이션

### 정의

`@GetMapping`은 HTTP GET 요청을 처리하는 데 사용됩니다. 일반적으로 데이터를 조회하거나 특정 리소스의 정보를 가져오는 데 활용됩니다.

### 예제 코드

```java
@RestController
@RequestMapping("/api")
public class ExampleController {

    @GetMapping("/users")
    public List<String> getUsers() {
        return List.of("Alice", "Bob", "Charlie");
    }

    @GetMapping("/users/{id}")
    public String getUserById(@PathVariable String id) {
        return "User " + id;
    }
}
```

### 특징

- 클라이언트가 데이터를 요청할 때 사용
- 파라미터는 URL 경로 또는 쿼리 문자열로 전달 가능
- 서버 상태를 변경하지 않음

---

## `@PostMapping`: 새로운 데이터 생성을 위한 어노테이션

### 정의

`@PostMapping`은 HTTP POST 요청을 처리하며, 주로 새로운 데이터를 생성하거나 리소스를 추가하는 데 사용됩니다.

### 예제 코드

```java
@RestController
@RequestMapping("/api")
public class ExampleController {

    @PostMapping("/users")
    public String createUser(@RequestBody String name) {
        return "User " + name + " created!";
    }
}
```

### 특징

- 요청 본문에 데이터를 포함하여 전송
- 보통 데이터베이스에 새로운 레코드를 추가하는 작업에 활용
- idempotent(멱등성)가 아님

---

## `@PutMapping`: 데이터 수정을 위한 어노테이션

### 정의

`@PutMapping`은 HTTP PUT 요청을 처리하며, 주로 기존 데이터를 업데이트하는 데 사용됩니다.

### 예제 코드

```java
@RestController
@RequestMapping("/api")
public class ExampleController {

    @PutMapping("/users/{id}")
    public String updateUser(@PathVariable String id, @RequestBody String newName) {
        return "User " + id + " updated to " + newName;
    }
}
```

### 특징

- 데이터 전체를 교체하거나 수정
- 요청 데이터가 자원의 상태를 표현해야 함
- idempotent(멱등성)를 보장

---

## `@DeleteMapping`: 데이터 삭제를 위한 어노테이션

### 정의

`@DeleteMapping`은 HTTP DELETE 요청을 처리하며, 주로 특정 데이터를 삭제하는 데 사용됩니다.

### 예제 코드

```java
@RestController
@RequestMapping("/api")
public class ExampleController {

    @DeleteMapping("/users/{id}")
    public String deleteUser(@PathVariable String id) {
        return "User " + id + " deleted!";
    }
}
```

### 특징

- 서버 자원을 제거
- 보통 URL 경로 변수로 삭제 대상 지정
- idempotent(멱등성)를 보장

---

## HTTP 메서드별 어노테이션 비교

| HTTP 메서드 | 어노테이션       | 주요 목적            | 멱등성 |
|-------------|------------------|----------------------|--------|
| GET         | `@GetMapping`    | 데이터 조회          | O      |
| POST        | `@PostMapping`   | 새로운 데이터 생성   | X      |
| PUT         | `@PutMapping`    | 데이터 전체 수정     | O      |
| DELETE      | `@DeleteMapping` | 데이터 삭제          | O      |

---

## 결론

Spring의 `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping` 어노테이션은 RESTful API 구현을 더욱 쉽고 간결하게 만들어 줍니다. 각 어노테이션의 목적과 특징을 잘 이해하고 활용하면 유지보수성과 확장성이 뛰어난 API를 설계할 수 있습니다.

---

**추가 팁:**  
RESTful API를 설계할 때는 HTTP 메서드의 의미를 잘 이해하고, 적절한 응답 코드와 메시지를 반환하도록 작성하는 것이 중요합니다. 😊