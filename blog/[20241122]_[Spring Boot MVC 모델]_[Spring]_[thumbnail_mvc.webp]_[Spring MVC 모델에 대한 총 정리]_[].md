# Spring Boot MVC 모델이란 ?

Spring Boot MVC(Model-View-Controller)는 Spring Framework의 핵심 아키텍처 중 하나로, 웹 애플리케이션을 효율적으로 설계하고 개발하기 위한 구조를 제공합니다. 

Spring Boot MVC는 웹 애플리케이션을 **Model**, **View**, **Controller** 세 가지 주요 구성 요소로 나누어 효율적으로 관리할 수 있게 설계되었습니다. 각각의 구성 요소는 서로 독립적이면서도 상호작용하여 애플리케이션의 핵심 기능을 제공합니다

<br />
<br />
<br />

## Model, View, Controller

### 1. Model (모델)

#### **역할**
- **비즈니스 로직**과 **데이터 처리**를 담당
- 데이터의 상태를 표현하며, 데이터베이스에서 데이터를 가져오거나 저장하는 작업을 포함
- Controller나 View와 독립적으로 동작하며 재사용 가능

#### **구현 방법**
- 일반적으로 **POJO (Plain Old Java Object)** 로 작성
- JPA, Hibernate, MyBatis 등을 통해 데이터베이스와 상호작용.
- 비즈니스 로직을 캡슐화하여 데이터를 처리

#### **예제**
```java
package com.example.mvcapp.model;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private double price;

    // Getters and setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }
}
```

<br />

### **2. View (뷰)**

#### **역할**

- **사용자에게 데이터를 시각적으로 표현**.
- Model 데이터를 기반으로 HTML, JSON, XML 등으로 데이터를 렌더링
- 사용자와 상호작용(UI)을 제공하며, Controller로부터 전달받은 데이터를 표시

#### **구현 방법**
- Thymeleaf, JSP, FreeMarker, Mustache 등 템플릿 엔진을 사용하여 View를 구현
- Model로부터 전달받은 데이터를 바인딩하여 동적인 UI를 생성

#### **예제 (Thymeleaf)**
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Product List</title>
</head>
<body>
    <h1>Product List</h1>
    <table>
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Price</th>
        </tr>
        <tr th:each="product : ${products}">
            <td th:text="${product.id}"></td>
            <td th:text="${product.name}"></td>
            <td th:text="${product.price}"></td>
        </tr>
    </table>
</body>
</html>
```

<br />

### **3. Controller (컨트롤러)**

#### **역할**
- 사용자로부터 요청을 받아 처리하고, 적절한 데이터를 Model에 담아 View로 전달
- Service 계층을 호출하여 비즈니스 로직을 처리
- View에 전달할 데이터를 준비하고 View의 선택을 제어

#### **구현 방법**
- Spring Boot에서는 `@Controller` 또는 `@RestController`를 사용하여 Controller를 구현
- 요청 매핑을 위해 `@GetMapping`, `@PostMapping`, `@RequestMapping` 등 애노테이션 사용

#### **예제**
```java
package com.example.mvcapp.controller;

import com.example.mvcapp.model.Product;
import com.example.mvcapp.service.ProductService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.List;

@Controller
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping("/products")
    public String getAllProducts(Model model) {
        // Service에서 데이터 가져오기
        List<Product> products = productService.getAllProducts();

        // Model에 데이터 추가
        model.addAttribute("products", products);

        // View 이름 반환
        return "productList"; // productList.html
    }
}
```

<br />
<br />
<br />

## Spring Boot MVC의 주요 구성 요소

### **1. DispatcherServlet**

#### **역할**
- Spring MVC의 **프론트 컨트롤러** 역할을 수행
- 클라이언트 요청을 받아 적절한 **Handler**(Controller)로 라우팅
- 요청 처리 후 최종적으로 응답을 클라이언트에 반환

#### **동작 과정**
1. HTTP 요청을 수신
2. 적절한 **HandlerMapping**을 통해 요청을 처리할 Controller를 찾음
3. Controller가 요청을 처리한 결과를 **ModelAndView** 형태로 반환
4. ViewResolver를 통해 View를 결정하고 렌더링
5. 클라이언트로 응답 반환

#### **특징**
- `DispatcherServlet`은 모든 요청을 중앙 집중적으로 처리하므로 로깅, 보안, 필터링 등의 공통 처리를 쉽게 수행 가능.

<br />

### **2. HandlerMapping**

#### **역할**
- 요청 URL과 적절한 **Handler**(주로 Controller 메서드)를 매핑
- 요청에 따라 어떤 Controller 메서드를 호출할지 결정

#### **주요 구현체**
- **`RequestMappingHandlerMapping`**:
  - `@RequestMapping`, `@GetMapping`, `@PostMapping` 등의 애노테이션 기반으로 요청을 매핑
- **`SimpleUrlHandlerMapping`**:
  - XML 또는 Java Config를 통해 URL을 명시적으로 매핑

#### **예제**
```java
@GetMapping("/hello")
public String sayHello() {
    return "hello";
}
```
- 위 코드는 `/hello` URL 요청을 처리하도록 `sayHello` 메서드를 매핑.

<br />

### **3. HandlerAdapter**

#### **역할**
- HandlerMapping이 찾아낸 Controller(Handler)를 호출하는 역할
- 다양한 유형의 Controller를 처리할 수 있도록 어댑터 패턴을 사용

#### **주요 구현체**
- **`RequestMappingHandlerAdapter`**:
  - 애노테이션 기반의 Controller(`@Controller`)를 처리
- **`SimpleControllerHandlerAdapter`**:
  - Spring의 구형 Controller 인터페이스를 처리
- **`HttpRequestHandlerAdapter`**:
  - `HttpRequestHandler`를 구현한 클래스를 처리

#### **특징**
HandlerAdapter는 다양한 유형의 Controller를 일관된 방식으로 처리할 수 있게 지원하여 확장성과 유연성을 제공합니다.

<br />

### **4. Controller**

#### **역할**
- 요청을 처리하고, 데이터를 준비하여 View로 전달
- Service 계층과 통신하여 비즈니스 로직을 수행
- 처리 결과를 `Model` 객체에 담아 반환

#### **구현 방법**
- Spring MVC에서는 `@Controller` 또는 `@RestController` 애노테이션을 사용
- 요청 매핑은 `@RequestMapping`, `@GetMapping`, `@PostMapping` 등으로 지정

#### **예제**
```java
@Controller
public class HelloController {

    @GetMapping("/hello")
    public String hello(Model model) {
        model.addAttribute("message", "Hello, Spring MVC!");
        return "hello"; // View 이름
    }
}
```
- 클라이언트가 `/hello` 요청을 보내면 `hello` 메서드가 실행되어 View로 데이터를 전달.

<br />

### **5. Model**

#### **역할**
- Controller와 View 간의 데이터 전달을 담당
- 데이터를 캡슐화하여 View에서 사용 가능하도록 제공

#### **구현 방법**
- `Model` 객체를 통해 데이터를 View로 전달
- POJO 클래스를 데이터 구조로 활용

#### **예제**
```java
public class Product {
    private String name;
    private double price;

    // Getters and Setters
}
```

Controller에서 `Model`을 사용해 데이터를 View로 전달:
```java
@GetMapping("/product")
public String product(Model model) {
    Product product = new Product();
    product.setName("Laptop");
    product.setPrice(1200.0);

    model.addAttribute("product", product);
    return "product"; // product.html
}
```

View에서 데이터 사용:
```html
<p th:text="${product.name}">Product Name</p>
<p th:text="${product.price}">Product Price</p>
```

<br />

### **6. ViewResolver**

#### **역할**
- Controller에서 반환한 View 이름을 실제 View 파일과 매핑
- HTML, JSP, FreeMarker 등의 템플릿 파일 경로를 결정

#### **주요 구현체**
- **`InternalResourceViewResolver`**:
  - JSP 파일을 View로 사용
- **`ThymeleafViewResolver`**:
  - Thymeleaf 템플릿 엔진과 통합
- **`FreeMarkerViewResolver`**:
  - FreeMarker 템플릿을 View로 사용

#### **설정 예제**
Thymeleaf를 사용하는 경우:
```properties
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```

<br />

### **7. View**

#### **역할**
- 최종적으로 클라이언트에게 렌더링되는 사용자 인터페이스를 생성
- Controller에서 전달된 Model 데이터를 활용하여 동적인 콘텐츠를 생성

#### **구현 방법**
- Thymeleaf, JSP, FreeMarker, Mustache 등 다양한 템플릿 엔진을 사용

#### **예제 (Thymeleaf)**
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello</title>
</head>
<body>
    <h1 th:text="${message}">Placeholder</h1>
</body>
</html>
```
- Model에 추가된 `message` 데이터를 사용하여 동적인 콘텐츠 생성

<br />

### **8. ModelAndView**

#### **역할**
- Controller가 처리 결과를 반환할 때, Model과 View를 함께 담아 전달
- View 이름과 Model 데이터를 포함

#### **예제**
```java
@GetMapping("/example")
public ModelAndView example() {
    ModelAndView mav = new ModelAndView("example");
    mav.addObject("key", "value");
    return mav;
}
```

<br />
<br />
<br />

## 3. **Spring Boot MVC 설정**

Spring Boot MVC 설정은 **웹 애플리케이션 개발**을 위한 핵심 단계로, 개발자는 **자동 설정** 덕분에 복잡한 구성 없이도 빠르게 애플리케이션을 구축할 수 있습니다. 기본 설정은 Spring Boot가 제공하지만, 특정 요구사항에 맞게 세부 조정을 할 수도 있습니다. 여기서는 Spring Boot MVC 설정의 주요 요소와 세부 사항을 자세히 다룹니다.

<br />

### **1. 기본 Spring Boot MVC 설정**
Spring Boot는 Spring MVC를 기본적으로 활성화하며, 자동 설정을 통해 대부분의 설정을 처리합니다. 

#### **자동 구성된 DispatcherServlet**
Spring Boot는 `DispatcherServlet`을 자동으로 등록하고, 모든 요청(`/*`)을 처리하도록 설정합니다.

- **Default Servlet Mapping**:
  - Spring Boot의 `DispatcherServlet`은 기본적으로 모든 URL에 매핑되어 요청을 처리합니다.
  - `application.properties`에서 변경 가능:
    ```properties
    spring.mvc.servlet.path=/api
    ```

#### **View Resolver 자동 설정**
Spring Boot는 템플릿 엔진별로 기본적인 View Resolver 설정을 제공합니다.

- **Thymeleaf** (기본 엔진):
  - 기본적으로 `/resources/templates/` 디렉터리에 `.html` 파일이 View로 매핑됩니다.
  - `application.properties` 설정:
    ```properties
    spring.thymeleaf.prefix=classpath:/templates/
    spring.thymeleaf.suffix=.html
    ```

- **JSP**:
  - JSP 파일은 `/WEB-INF/views/` 디렉터리에 매핑됩니다.
    ```properties
    spring.mvc.view.prefix=/WEB-INF/views/
    spring.mvc.view.suffix=.jsp
    ```

#### **Static Resources 설정**
- 기본적으로 `/static`, `/public`, `/resources`, `/META-INF/resources` 디렉터리에 있는 정적 파일(css, js 등)을 자동으로 제공.
- 디렉터리 변경:
  ```properties
  spring.web.resources.static-locations=classpath:/custom-static/
  ```

<br />

### **2. Spring Boot MVC 주요 설정 항목**
`application.properties` 또는 `application.yml` 파일을 통해 Spring Boot MVC의 동작을 세부적으로 조정할 수 있습니다.

#### **(1) 기본 설정**
```properties
# DispatcherServlet 기본 경로
spring.mvc.servlet.path=/api

# 정적 리소스 경로
spring.web.resources.static-locations=classpath:/static/, classpath:/media/

# 콘텐츠 캐싱
spring.web.resources.cache.period=3600

# ViewResolver 설정 (Thymeleaf 예)
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.cache=false
```

#### **(2) Content Negotiation (콘텐츠 협상)**
Spring MVC는 요청의 **Accept 헤더**나 URL 확장자를 기반으로 클라이언트가 원하는 응답 형식을 결정합니다.

- 설정:
  ```properties
  spring.mvc.contentnegotiation.favor-path-extension=true
  spring.mvc.contentnegotiation.media-types.json=application/json
  spring.mvc.contentnegotiation.media-types.xml=application/xml
  ```

- 사용 예:
  - `/example.json`: JSON 응답.
  - `/example.xml`: XML 응답.

<br />

### **3. 세부 설정**

#### **(1) CORS 설정 (Cross-Origin Resource Sharing)**
다른 도메인에서 리소스를 요청할 수 있도록 설정.

- 전역 설정:
  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
      @Override
      public void addCorsMappings(CorsRegistry registry) {
          registry.addMapping("/**") // 모든 경로 허용
                  .allowedOrigins("http://example.com") // 특정 도메인만 허용
                  .allowedMethods("GET", "POST", "PUT", "DELETE");
      }
  }
  ```

#### **(2) Message Converters**
Spring MVC는 요청/응답 데이터를 JSON, XML 등 다양한 포맷으로 변환하기 위해 **HttpMessageConverter**를 사용합니다.

- Spring Boot는 Jackson을 사용하여 JSON을 기본 변환기로 제공.
- 커스터마이징:
  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
      @Override
      public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
          MappingJackson2HttpMessageConverter jsonConverter = new MappingJackson2HttpMessageConverter();
          converters.add(jsonConverter);
      }
  }
  ```

#### **(3) Exception Handling**
Spring Boot MVC는 예외 처리를 간소화하기 위해 기본적인 예외 핸들러를 제공합니다.

- 사용자 정의 예외 처리:
  ```java
  @ControllerAdvice
  public class GlobalExceptionHandler {

      @ExceptionHandler(ResourceNotFoundException.class)
      public ResponseEntity<String> handleNotFound(ResourceNotFoundException ex) {
          return new ResponseEntity<>(ex.getMessage(), HttpStatus.NOT_FOUND);
      }
  }
  ```

#### **(4) Locale 및 국제화 설정**
- 다국어 지원을 위한 설정:
  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
      @Bean
      public LocaleResolver localeResolver() {
          SessionLocaleResolver slr = new SessionLocaleResolver();
          slr.setDefaultLocale(Locale.ENGLISH);
          return slr;
      }

      @Bean
      public LocaleChangeInterceptor localeChangeInterceptor() {
          LocaleChangeInterceptor interceptor = new LocaleChangeInterceptor();
          interceptor.setParamName("lang");
          return interceptor;
      }

      @Override
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(localeChangeInterceptor());
      }
  }
  ```

<br />

### **4. Spring Boot MVC 확장**
Spring Boot MVC는 기본 설정 외에도 확장을 통해 다양한 요구사항을 충족할 수 있습니다.

#### **(1) WebMvcConfigurer**
`WebMvcConfigurer`를 구현하여 MVC 설정을 커스터마이징 가능.

- 예제:
  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {

      @Override
      public void addViewControllers(ViewControllerRegistry registry) {
          registry.addViewController("/login").setViewName("login");
      }

      @Override
      public void addResourceHandlers(ResourceHandlerRegistry registry) {
          registry.addResourceHandler("/files/**")
                  .addResourceLocations("file:/uploads/");
      }
  }
  ```

#### **(2) Interceptors**
요청/응답의 흐름을 가로채 특정 작업을 수행.

- Interceptor 추가:
  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(new CustomInterceptor())
                  .addPathPatterns("/secure/**")
                  .excludePathPatterns("/public/**");
      }
  }
  ```

<br />

### **5. 동작 예제**
#### **`application.properties` 설정**
```properties
# DispatcherServlet 매핑
spring.mvc.servlet.path=/api

# 정적 리소스
spring.web.resources.static-locations=classpath:/static/

# Thymeleaf ViewResolver
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.cache=false
```

#### **Controller**
```java
@Controller
public class HomeController {
    @GetMapping("/home")
    public String home(Model model) {
        model.addAttribute("message", "Welcome to Spring Boot MVC!");
        return "home";
    }
}
```

#### **View (Thymeleaf Template)**
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Home</title>
</head>
<body>
    <h1 th:text="${message}">Message Placeholder</h1>
</body>
</html>
```

#### **정적 리소스**
- `/static/css/style.css`와 같은 파일은 자동으로 서빙됩니다.

<br />
<br />
<br />

## 4. **Spring Boot MVC의 동작 흐름

1. **HTTP 요청 수신**
   - 클라이언트(웹 브라우저, REST 클라이언트 등)가 HTTP 요청(예: `GET /products`)을 보냅니다.
   - 이 요청은 **DispatcherServlet**이 수신합니다. Spring Boot는 기본적으로 모든 요청을 `DispatcherServlet`에 매핑(`/*`)합니다.

<br />

2. **HandlerMapping을 통해 적절한 Handler(Controller) 찾기**
   - **DispatcherServlet**은 `HandlerMapping`을 사용하여 요청 URL과 매핑된 Controller를 검색합니다.
   - `HandlerMapping`은 요청 URL 패턴(`@GetMapping`, `@RequestMapping`)과 Controller 메서드의 매핑 정보를 관리합니다.

   **예:**
   ```java
   @RestController
   @RequestMapping("/products")
   public class ProductController {
       @GetMapping
       public List<Product> getAllProducts() {
           return productService.getAllProducts();
       }
   }
   ```
   - 클라이언트가 `GET /products`를 요청하면, `HandlerMapping`은 `getAllProducts` 메서드와 매핑됩니다.

<br />

3. **HandlerAdapter가 Controller 호출**
   - `HandlerMapping`이 적절한 Controller를 찾은 뒤, `HandlerAdapter`가 해당 Handler(Controller)를 호출합니다.
   - 이 과정에서 요청 파라미터(예: `?id=123`)를 적절한 형태로 바인딩하고, 필요한 데이터를 준비합니다.

   **예:**
   ```java
   @GetMapping("/{id}")
   public Product getProductById(@PathVariable Long id) {
       return productService.getProductById(id);
   }
   ```
   - URL `GET /products/123` 요청에서 `123`은 `id` 변수로 바인딩되어 메서드가 호출됩니다.

<br />

4. **Controller에서 비즈니스 로직 실행**
   - Controller는 Service 계층을 호출하여 비즈니스 로직을 처리합니다.
   - Service는 데이터를 검색하거나 처리한 후 결과를 반환합니다.

   **예:**
   ```java
   @Service
   public class ProductService {
       public Product getProductById(Long id) {
           return productRepository.findById(id)
                   .orElseThrow(() -> new ProductNotFoundException("Product not found"));
       }
   }
   ```

   - `getProductById` 메서드는 데이터베이스에서 제품을 검색하거나, 예외를 던질 수 있습니다.

<br />

5. **ModelAndView 또는 Model 반환**
   - Controller가 처리 결과를 Model에 담거나, REST 응답(JSON, XML)을 반환합니다.

   **HTML View 예:**
   ```java
   @GetMapping("/welcome")
   public String welcome(Model model) {
       model.addAttribute("message", "Welcome to Spring Boot MVC!");
       return "welcome"; // View 이름
   }
   ```
   - `Model` 객체는 데이터를 View로 전달하며, `welcome.html` View 파일이 선택됩니다.

   **REST API 예:**
   ```java
   @GetMapping("/{id}")
   public ResponseEntity<Product> getProductById(@PathVariable Long id) {
       return ResponseEntity.ok(productService.getProductById(id));
   }
   ```
   - REST 응답에서는 JSON 형태로 데이터가 반환됩니다.

<br />

6. **ViewResolver를 통해 View 선택**
   - HTML View의 경우, `ViewResolver`가 View 이름(예: `welcome`)을 실제 파일 경로로 매핑합니다.
   - Spring Boot는 기본적으로 Thymeleaf를 사용하며, 설정된 디렉터리에서 View를 찾습니다.

   **예:**
   ```properties
   spring.thymeleaf.prefix=classpath:/templates/
   spring.thymeleaf.suffix=.html
   ```

   - `welcome` View는 `/templates/welcome.html` 파일에 매핑됩니다.

<br />

7. **View 렌더링 및 응답 생성**
   - View는 `Model` 데이터를 활용하여 클라이언트에게 표시할 최종 HTML을 생성합니다.
   - REST 응답의 경우, `HttpMessageConverter`가 데이터를 JSON 또는 XML로 변환하여 반환합니다.

   **HTML 렌더링 예:**
   ```html
   <h1 th:text="${message}">Placeholder</h1>
   ```

   **JSON 응답 예:**
   ```json
   {
       "id": 1,
       "name": "Laptop",
       "price": 1200.00
   }
   ```

<br />

8. **클라이언트로 응답 반환**
   - View에서 생성된 HTML, JSON, XML 등이 HTTP 응답으로 클라이언트에 전달됩니다.
   - 클라이언트는 받은 데이터를 화면에 출력하거나 후속 작업을 진행합니다.

<br />

### **Spring Boot MVC 동작 흐름 예제**

#### **1. 요청**
- URL: `GET /products`
- 요청 처리: `DispatcherServlet`

#### **2. Controller 처리**
```java
@GetMapping("/products")
public String products(Model model) {
    List<Product> products = productService.getAllProducts();
    model.addAttribute("products", products);
    return "productList";
}
```

#### **3. View 선택**
- `ViewResolver`는 `productList`를 `/templates/productList.html`로 매핑.

#### **4. View 렌더링**
```html
<table>
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Price</th>
    </tr>
    <tr th:each="product : ${products}">
        <td th:text="${product.id}"></td>
        <td th:text="${product.name}"></td>
        <td th:text="${product.price}"></td>
    </tr>
</table>
```

#### **5. 최종 HTML**
```html
<table>
    <tr>
        <th>ID</th>
        <th>Name</th>
        <th>Price</th>
    </tr>
    <tr>
        <td>1</td>
        <td>Laptop</td>
        <td>1200.00</td>
    </tr>
    <tr>
        <td>2</td>
        <td>Smartphone</td>
        <td>800.00</td>
    </tr>
</table>
```

#### **6. 클라이언트에 반환**
- 렌더링된 HTML이 클라이언트(웹 브라우저)에 반환되고, 결과가 화면에 표시됩니다.

<br />

### **Spring Boot MVC 동작 요약**
1. 클라이언트 요청 → DispatcherServlet 수신.
2. HandlerMapping → Controller 메서드 찾기.
3. Controller → 비즈니스 로직(Service 계층) 호출.
4. 결과를 Model에 저장하거나 직접 JSON으로 응답.
5. ViewResolver가 View 선택.
6. View에서 최종 렌더링 수행.
7. 응답 반환.

Spring Boot MVC의 이러한 동작 방식은 확장성과 유지보수성을 제공하며, 웹 애플리케이션 개발을 더욱 간편하게 만듭니다.

<br />
<br />
<br />


## 5. **Spring Boot MVC의 장점**

### **1. 생산성**
Spring Boot MVC는 개발자가 신속하게 웹 애플리케이션을 구축할 수 있도록 다양한 생산성 도구와 기능을 제공합니다.

#### **(1) 자동 설정**
- 대부분의 설정을 자동으로 처리하므로, 초기 개발에 필요한 설정 부담이 줄어듭니다.
- **예**: DispatcherServlet, ViewResolver 등이 자동 구성됩니다.

#### **(2) 빠른 시작**
- `Spring Initializr`와 같은 도구를 통해 프로젝트를 몇 분 안에 시작 가능.
- 종속성을 선택하면 자동으로 프로젝트에 적용됩니다.

#### **(3) 즉시 실행 가능**
- Spring Boot는 내장된 Tomcat, Jetty, Undertow 서버를 제공하여 애플리케이션을 실행 가능한 JAR 파일로 패키징 가능.
- 복잡한 배포 과정 없이 `java -jar app.jar`로 애플리케이션 실행 가능.

#### **(4) 최소한의 코드로 기본 기능 제공**
- RESTful API를 간단하게 개발 가능.
- 예:
  ```java
  @RestController
  public class HelloController {
      @GetMapping("/hello")
      public String sayHello() {
          return "Hello, Spring Boot MVC!";
      }
  }
  ```

<br />

### **2. 유지보수성**
Spring Boot MVC는 모듈화된 설계와 표준화된 구조를 통해 유지보수성을 크게 향상시킵니다.

#### **(1) MVC 아키텍처 기반**
- Model, View, Controller로 구성되어 각 계층의 역할이 명확하게 분리됩니다.
- 코드를 수정하거나 확장할 때 다른 계층에 미치는 영향을 최소화.

#### **(2) 표준화된 프로젝트 구조**
- Spring Boot는 표준 프로젝트 디렉터리 구조를 권장하여 일관된 코드베이스를 유지.
- **디렉터리 예**:
  ```
  src/main/java/com/example/mvcapp/
  ├── controller/
  ├── model/
  ├── service/
  ├── repository/
  └── Application.java
  ```

#### **(3) 테스트 지원**
- Spring Boot MVC는 단위 테스트와 통합 테스트를 쉽게 작성할 수 있는 도구를 제공합니다.
- **예**: `MockMvc`를 사용한 Controller 테스트:
  ```java
  @Test
  public void testHelloEndpoint() throws Exception {
      mockMvc.perform(get("/hello"))
             .andExpect(status().isOk())
             .andExpect(content().string("Hello, Spring Boot MVC!"));
  }
  ```

<br />

### **3. 확장성**
Spring Boot MVC는 애플리케이션의 요구사항이 증가해도 쉽게 확장할 수 있는 유연한 아키텍처를 제공합니다.

#### **(1) 모듈화된 Spring 생태계와 통합**
- Spring Security, Spring Data, Spring Batch 등과 쉽게 통합 가능.
- **예**: Spring Security를 사용한 인증/인가 추가:
  ```java
  @Configuration
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
      @Override
      protected void configure(HttpSecurity http) throws Exception {
          http.authorizeRequests()
              .antMatchers("/admin/**").authenticated()
              .and()
              .formLogin();
      }
  }
  ```

#### **(2) 커스터마이징 가능**
- 기본적으로 제공되는 설정을 커스터마이징하여 애플리케이션의 요구사항에 맞게 조정 가능.
- **예**: JSON 변환기(Jackson) 커스터마이징:
  ```java
  @Bean
  public ObjectMapper objectMapper() {
      return new ObjectMapper().enable(SerializationFeature.INDENT_OUTPUT);
  }
  ```

#### **(3) 분산 시스템 및 마이크로서비스 지원**
- Spring Boot MVC는 Spring Cloud와 통합되어 마이크로서비스 아키텍처를 쉽게 구축 가능.
- Eureka, Ribbon, Feign 등과 통합하여 분산 시스템 환경에서 사용할 수 있습니다.

<br />

### **4. 유연성**
Spring Boot MVC는 다양한 요구사항과 기술 스택을 지원하여 유연성을 제공합니다.

#### **(1) 다중 View 지원**
- HTML, JSON, XML, PDF, Excel 등 다양한 View를 생성 가능.
- **예**: JSON 응답:
  ```java
  @GetMapping("/json")
  public ResponseEntity<Map<String, String>> getJson() {
      Map<String, String> response = new HashMap<>();
      response.put("key", "value");
      return ResponseEntity.ok(response);
  }
  ```

#### **(2) 템플릿 엔진 선택 가능**
- Thymeleaf, JSP, FreeMarker, Mustache 등 다양한 템플릿 엔진을 지원.
- **예**: Thymeleaf를 사용하는 경우:
  ```html
  <h1 th:text="${message}">Placeholder</h1>
  ```

#### **(3) RESTful API 및 웹 소켓 지원**
- Spring Boot MVC는 RESTful API와 WebSocket을 모두 쉽게 구현할 수 있습니다.
- **RESTful 예제**:
  ```java
  @RestController
  @RequestMapping("/api")
  public class ApiController {
      @GetMapping("/users")
      public List<User> getUsers() {
          return userService.getAllUsers();
      }
  }
  ```

<br />

### **5. 커뮤니티와 문서**
Spring Boot MVC는 넓은 커뮤니티와 풍부한 문서화를 통해 개발자에게 강력한 지원을 제공합니다.

#### **(1) 방대한 공식 문서**
- Spring 공식 문서와 가이드는 각종 기능과 설정을 상세히 설명.

#### **(2) 커뮤니티 지원**
- Stack Overflow, GitHub Issues 등에서 활발한 커뮤니티 활동.
- 문제가 발생했을 때 해결책을 쉽게 찾을 수 있음.

<br />

### **6. 성능 최적화**
Spring Boot MVC는 기본적으로 성능에 최적화된 설정을 제공하며, 추가 설정을 통해 더 높은 성능을 달성할 수 있습니다.

#### **(1) 내장 캐싱**
- 정적 리소스나 View 템플릿의 캐싱을 자동으로 처리.

#### **(2) 비동기 요청 처리**
- 비동기 컨트롤러를 쉽게 구현 가능.
- **예**: 비동기 요청 처리:
  ```java
  @GetMapping("/async")
  public CompletableFuture<String> asyncEndpoint() {
      return CompletableFuture.supplyAsync(() -> "Async Response");
  }
  ```

<br />

### **7. 안정성**
Spring Boot MVC는 성숙한 프레임워크로, 안정적인 운영 환경을 제공합니다.

#### **(1) 로깅 및 모니터링**
- 기본적으로 Spring Boot Actuator를 제공하여 애플리케이션 상태를 모니터링.
- **예**: `/actuator/health` 엔드포인트로 애플리케이션 상태 확인.

#### **(2) 에러 핸들링**
- 글로벌 예외 처리 기능 제공.
- **예**:
  ```java
  @ControllerAdvice
  public class GlobalExceptionHandler {
      @ExceptionHandler(Exception.class)
      public ResponseEntity<String> handleException(Exception ex) {
          return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(ex.getMessage());
      }
  }
  ```

<br />

### **종합**
Spring Boot MVC는 **생산성**, **확장성**, **유연성**, **안정성**, **성능 최적화**를 고루 갖춘 프레임워크입니다. 이를 통해 개발자는 효율적으로 웹 애플리케이션을 설계하고 배포할 수 있으며, 높은 수준의 유지보수성을 제공합니다.

<br />
<br />
<br />

---

#### Spring Boot MVC는 간결함과 강력함을 겸비한 웹 애플리케이션 개발 프레임워크로, 복잡한 설정을 최소화하면서도 확장성과 유연성을 제공합니다.  
#### 다양한 템플릿 엔진과 RESTful API 지원을 통해 현대적인 웹 서비스 구축을 손쉽게 돕는 한편, 방대한 커뮤니티와 풍부한 문서로 개발자에게 강력한 지원을 제공합니다.  
#### 생산성과 유지보수성, 그리고 안정성을 모두 만족시키는 Spring Boot MVC는 초보 개발자부터 전문가까지 누구에게나 추천할 수 있는 프레임워크입니다.  
#### Web 개발의 시작을 Spring Boot MVC와 함께하세요!