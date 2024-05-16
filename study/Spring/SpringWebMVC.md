---
title: Spring Web MVC
description: 
published: true
date: 2024-05-16T14:47:14.297Z
tags: 
editor: markdown
dateCreated: 2024-05-16T14:47:14.297Z
---

## Spring MVC 구성요소(DispatcherSerlvet에서 사용하는 여러 인터페이스)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/6f1d7a23-905a-4fde-ab88-874c96c7ab09/c2fad99e-73a2-4e50-9d74-6efc75e363b9/Untitled.png)

DispatcherServlet의 기본 전략

- DispatcherServlet.properties

별도 설정

- dispatcher-servlet.xml

MultipartResolver

- 파일 업로드 요청 처리에 필요한 인터페이스
- HttpServletRequest를 MultipartHttpServletRequest로 변환해주어 요청이 담고 있는 File을 꺼낼 수 있는 API 제공.
- ex) egovframework.coviframework.util.XSSMultipartResolver

LocaleResolver

- 요청을 처음 분석하는 단계에서 사용
- ex) org.springframework.web.servlet.i18n.SessionLocaleResolver
- 클라이언트의 위치(Locale) 정보를 파악하여 지역 언어를 resolving
- 기본 전략은 요청의 accept-language를 보고 판단.

ThemeResolver

- 애플리케이션에 설정된(요청된) 테마를 파악하고 변경할 수 있는 인터페이스 (css 등)
- 제품에서 사용하지 않고 세션의 코드를 통해서 테마 관리
- 참고: https://memorynotfound.com/spring-mvc-theme-switcher-example/

HandlerMapping

- 요청을 처리할 핸들러(controller)를 찾는 인터페이스
- ex) org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping

HandlerAdapter

- HandlerMapping이 찾아낸 “핸들러”(controller)를 처리(실행)하는 인터페이스
- ex) org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter

HandlerExceptionResolver

- 요청 처리 중에 발생한 에러 처리하는 인터페이스
- ex) egovframework.baseframework.exception.CoviSimpleMappingExceptionResolver

RequestToViewNameTranslator

- 핸들러에서 뷰 이름을 명시적으로 리턴하지 않은 경우, 요청을 기반으로 뷰 이름을 판단하는 인터페이스

ViewResolver

- 뷰 이름(string)에 해당하는 뷰를 찾아내는 인터페이스
- org.springframework.web.servlet.view.InternalResourceViewResolver

FlashMapManager

- FlashMap 인스턴스를 가져오고 저장하는 인터페이스
- FlashMap은 주로 Post 요청 처리 시 get 요청으로 리다이렉션을 사용할 때 요청 매개변수를 사용하지 않고 데이터를 전달하고 정리할 때 사용한다.
- sessionFlashMapManager
- 예시: https://memo-the-day.tistory.com/178

## **스프링 MVC 동작원리 정리**

스프링 부트 사용하지 않는 스프링 MVC

- 서블릿 컨네이너(톰캣)에 등록한 웹 애플리케이션(WAR)에 DispatcherServlet을 등록한다.
    
    ○ web.xml에 서블릿 등록 (제품)
    
    ○ 또는 WebApplicationInitializer에 자바 코드로 서블릿 등록 (스프링 3.1+, 서블릿 3.0+) 
    
- 세부 구성 요소 설정(dispatcher-servlet.xml)

스프링 부트를 사용하는 스프링 MVC

● 자바 애플리케이션에 내장 톰캣을 만들고 그 안에 DispatcherServlet을 등록한다. 

○ 스프링 부트 자동 설정이 자동으로 해줌.

● 스프링 부트의 주관에 따라 여러 인터페이스 구현체를 빈으로 등록한다.

https://velog.io/@gillog/Spring-DIDependency-Injection