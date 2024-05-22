---
title: Spring 에러/예외처리
description: 
published: true
date: 2024-05-22T13:47:58.325Z
tags: 
editor: markdown
dateCreated: 2024-05-22T13:47:58.325Z
---

# Spring 에러/예외 처리
## web.xml error-page

1. 컨트롤러 (예외 발생) → 인터셉터 → (디스패처)서블릿 → 필터 → WAS(에러 페이지 처리)
2. Controller에서 발생한 예외에 대한 처리를 안하게 되면 예외를 서블릿 컨테이너인 WAS(보통 톰캣)에 던져 에러 페이지를 통해 처리 
3. 즉, 에러페이지 == **예외 처리를 해줄 수 없는 영역에 대한 정보를 보여주기 위한 페이지**
4. 서블릿 컨테이너(톰캣)의 내장 에러 페이지를 커스텀 오류 페이지로 매핑


```xml
<error-page>
	<exception-type>java.lang.Throwable</exception-type>
	<location>/WEB-INF/views/cmmn/error/error_500.jsp</location>
</error-page>
<error-page>
	<error-code>401</error-code>
	<location>/WEB-INF/views/cmmn/error/error_401.jsp</location>
</error-page>

<error-page>
	<error-code>403</error-code>
	<location>/WEB-INF/views/cmmn/error/error_403.jsp</location>
</error-page>
<error-page>
	<error-code>404</error-code>
	<location>/WEB-INF/views/cmmn/error/error_400.jsp</location>
</error-page>
<error-page>
	<error-code>405</error-code>
	<location>/WEB-INF/views/cmmn/error/error_400.jsp</location>
</error-page>
<error-page>
	<error-code>500</error-code>
	<location>/WEB-INF/views/cmmn/error/error_500.jsp</location>
</error-page>
<error-page>
	<error-code>501</error-code>
	<location>/WEB-INF/views/cmmn/error/error_500.jsp</location>
</error-page>
<error-page>
	<error-code>502</error-code>
	<location>/WEB-INF/views/cmmn/error/error_500.jsp</location>
</error-page>
```

## HandlerExceptionResolver

<aside>
💡 Spring에서 공통된 에러 처리 로직을 메인 로직으로부터 분리하는 다양한 예외 처리 방식을 고안하였고, 예외 처리 전략을 추상화한 대표적인 인터페이스

</aside>

- HandlerExceptionResolver는 발생한 Exception을 catch하고 HTTP 상태나 응답 메세지 등을 설정
- WAS 입장에서는 해당 요청이 **정상적인 응답인 것으로 인식되어 에러 전달이 진행 되지 않는다**

### 종류

1. SimpleMappingExceptionResolver :전자정부프레임워크에선 대표적으로 사용
2. DefaultHandlerExceptionResolver : 스프링 내부의 기본 예외들을 처리
3. ResponseStatusExceptionResolver : HTTP 상태 코드를 지정
4. ExceptionHandlerExceptionResolver :에러 응답을 위한 Controller나 ControllerAdvice에 있는 ExceptionHandler를 처리(어노테이션)

```xml
<bean class="egovframework.baseframework.exception.CoviSimpleMappingExceptionResolver">
	<property name="defaultErrorView" value="cmmn/error/egovError" />
	<property name="exceptionMappings">
		<props>
			<prop key="org.springframework.web.multipart.MultipartException">cmmn/error/fileError</prop>
			<prop key="org.springframework.dao.DataAccessException">cmmn/error/dataAccessFailure</prop>
			<prop key="org.springframework.transaction.TransactionException">cmmn/error/transactionFailure</prop>
			<prop key="org.egovframe.rte.fdl.cmmn.exception.EgovBizException">cmmn/error/egovError</prop>
			<prop key="org.springframework.security.AccessDeniedException">cmmn/error/egovError</prop>
		</props>
	</property>
</bean>
```