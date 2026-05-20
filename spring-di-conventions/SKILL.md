---
name: spring-di-conventions
description: Codex가 Spring Boot와 Java 코드에서 컨트롤러, 서비스, 컴포넌트, 설정 클래스의 의존성 주입 코드를 생성, 수정, 리뷰할 때 사용하는 규칙 스킬이다. 기본은 `@RequiredArgsConstructor`와 `private final` 필드를 사용한 생성자 주입이며, 필드 주입과 불필요한 수동 생성자는 피하고, `record`, 프레임워크 제약, `@Value` 같은 예외는 `@Configuration` 또는 `@ConfigurationProperties` 중심으로 정리한다.
---

# Spring DI 규칙

## 개요

Spring 빈 클래스의 기본 DI 방식은 `@RequiredArgsConstructor`로 둔다.
주입받는 협력 객체는 `private final` 필드로 표현한다.
기본 규칙은 단순하게 유지한다. Lombok 기반 생성자 주입을 우선하고, 다른 방식은 이유가 있는 예외로 취급한다.

## 핵심 규칙

`@RestController`, `@Controller`, `@Service`, `@Component` 같은 Spring 관리 클래스에는 `@RequiredArgsConstructor`를 우선 사용한다.
주입받는 의존성은 `private final` 필드로 선언한다.
Lombok이 의존성 구조를 명확하게 표현할 수 있으면 생성자를 직접 쓰지 않는다.
한 클래스 안에서는 DI 스타일을 한 가지로 일관되게 유지한다.

필드 주입인 `@Autowired`는 피한다.
생성 후 변경이 꼭 필요한 프레임워크 요구가 없다면 setter 주입도 피한다.
`@RequiredArgsConstructor`가 대신할 수 있는 단순 수동 생성자는 만들지 않는다.

## 예외 규칙

`record`처럼 언어 차원에서 생성자가 해결되는 타입은 그대로 둔다.
상속 구조, 프레임워크 통합, 기타 구조적 제약 때문에 Lombok 생성자가 어색하거나 위험하면 수동 생성자를 허용한다.
빈 필드에 붙는 `@Value`는 `final` 기반 생성자 주입을 흐리게 만드는 신호로 본다.
`@Value`가 필요하면 먼저 설정 바인딩을 `@Configuration` 또는 `@ConfigurationProperties`로 옮기고, 그 결과 객체나 설정된 빈을 final 의존성으로 다시 주입하는 방향을 우선 제안한다.
더 나은 구조로 옮기기 어렵다면 혼합 스타일을 예외적으로 허용한다.

## 작업 지침

새 Spring 클래스를 만들 때는 처음부터 `private final` 필드와 `@RequiredArgsConstructor`를 기준으로 시작한다.
기존 Spring 코드를 수정할 때는 예외가 없으면 `@Autowired` 필드 주입과 단순 수동 생성자를 `@RequiredArgsConstructor` 방식으로 바꾼다.
Spring 코드를 리뷰할 때는 DI 스타일 혼용, mutable 주입 필드, 불필요한 생성자, 설정 바인딩으로 올려야 할 `@Value` 사용을 지적한다.
리팩터링을 제안할 때는 클래스를 다시 final 기반 생성자 주입으로 되돌리는 가장 작은 변경을 우선한다.

## 예시

다음 형태를 기본으로 선호한다.

```java
@Service
@RequiredArgsConstructor
public class OrderService {

    private final PaymentClient paymentClient;
    private final OrderRepository orderRepository;
}
```

다음 형태는

```java
@Service
public class OrderService {

    @Autowired
    private PaymentClient paymentClient;

    @Autowired
    private OrderRepository orderRepository;
}
```

가능하면 다음처럼 바꾼다.

```java
@Service
@RequiredArgsConstructor
public class OrderService {

    private final PaymentClient paymentClient;
    private final OrderRepository orderRepository;
}
```
