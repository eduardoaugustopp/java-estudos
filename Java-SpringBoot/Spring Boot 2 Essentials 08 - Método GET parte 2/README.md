## Spring Boot 2 Essentials 08 - Método GET parte 2

É uma boa prática retornar informações extras, como por exemplo o status dessa requisição coloca o `ResponseEntity` e método http que esta representando essa resposta `HttpStatus.OK`, quando faz isso precisa mudar assinatura do método;

Muitas das vezes vai ter `dois` tipos de `Get` um para `listar`, fazer um search e outro ao invés de retornar uma lista, retorna um produto específico baseado no `id` fornecido;

- É mais convenção do que regra, quando você tenta executar uma requisição para uma url passando o id e não encontra esse id muita gente retorna `404 status não encontrado`, esse status não tem muita informação; nesse caso o correto é usar `status 400 BAD_REQUEST`;

```java
.orElseThrow(() -> new ResponseStatusException(HttpStatus.BAD_REQUEST, "Prata not Found"));
```

```java
package br.com.pkpratas01.service;

import br.com.pkpratas01.domain.Prata;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Service;
import org.springframework.web.server.ResponseStatusException;

import java.util.List;

@Service
public class PrataService {
    private List<Prata> pratas = List.of(new Prata(1L, "Teste 1"), new Prata(2L, "Teste 2"));

    public List<Prata> listAll() {
        return pratas;
    }

    public Prata findById(long id) {
        return pratas.stream()
                .filter(prata -> prata.getId().equals(id))
                .findFirst()
                .orElseThrow(() -> new ResponseStatusException(HttpStatus.BAD_REQUEST, "Prata not Found"));
    }
}
```

Não é uma boa pratica passar todo esse stacktrace para o frontend, precisa esconder esses dados;

**http://localhost:8080/pratas/3**

```
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.

Thu Jan 16 09:24:43 BRT 2025
There was an unexpected error (type=Bad Request, status=400).
Prata not Found
org.springframework.web.server.ResponseStatusException: 400 BAD_REQUEST "Prata not Found"
	at br.com.pkpratas01.service.PrataService.lambda$1(PrataService.java:23)
	at java.base/java.util.Optional.orElseThrow(Optional.java:403)
	at br.com.pkpratas01.service.PrataService.findById(PrataService.java:23)
	at br.com.pkpratas01.controller.PrataController.findById(PrataController.java:34)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:257)
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:190)
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:118)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:986)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:891)
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1088)
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:978)
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1014)
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:903)
	at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:564)
```

Em `resources` cria um arquivo `application.yml`;

```yml
server:
  error:
    include-stacktrace: on_param
```

**http://localhost:8080/pratas/3?trace=true**

```
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.

Thu Jan 16 09:35:35 BRT 2025
There was an unexpected error (type=Bad Request, status=400).
Prata not Found
org.springframework.web.server.ResponseStatusException: 400 BAD_REQUEST "Prata not Found"
	at br.com.pkpratas01.service.PrataService.lambda$1(PrataService.java:23)
	at java.base/java.util.Optional.orElseThrow(Optional.java:403)
	at br.com.pkpratas01.service.PrataService.findById(PrataService.java:23)
	at br.com.pkpratas01.controller.PrataController.findById(PrataController.java:34)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:77)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:568)
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:257)
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:190)
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:118)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:986)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:891)
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1088)
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:978)
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1014)
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:903)
	at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:564)
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:885)
```

**http://localhost:8080/pratas/3**

```
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.

Thu Jan 16 09:36:46 BRT 2025
There was an unexpected error (type=Bad Request, status=400).
Prata not Found
```

- Se tiver duvida que tipo de SQL esta sendo gerado, `show-sql: true` - vai mostrar no console o que esta sendo executado

```yml
server:
  error:
    include-stacktrace: on_param

spring:
  datasource:
    url: jdbc:mysql://localhost:3307/prata?useSSL=false&createDatabaseIfNotExist=true&allowPublicKeyRetrieval=true
    username: root
    password: root
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
```
