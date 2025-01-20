## Spring Boot 2 Essentials 05 - Hot Swap com Spring Boot Devtools

**`CHATGPR`**

No Spring Boot 2 Essentials, o Hot Swap é uma técnica útil que permite atualizar o código da aplicação sem a necessidade de reiniciar o servidor completamente. Isso é possível graças ao Spring Boot Devtools, uma ferramenta que melhora a produtividade durante o desenvolvimento. Aqui estão os principais pontos sobre como usar o Hot Swap com Spring Boot Devtools:

1. Adicionando o Spring Boot Devtools no Projeto

Para habilitar o Hot Swap, você precisa adicionar a dependência spring-boot-devtools no arquivo pom.xml ou build.gradle do seu projeto.

No pom.xml:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
</dependency>
```

Configurações para Hot Swap

Você pode configurar o comportamento do Devtools para ajustar o que ele monitora e como lida com a recarga.

Exemplo no application.properties:

```bash
spring.devtools.restart.enabled=true
spring.devtools.restart.exclude=static/**,public/**
```
