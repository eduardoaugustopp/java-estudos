## Spring Boot 2 Essentials 25 - Sorting, Log SQL

`Sorting` - por padrão já tem o sorting;

**http://localhost:8080/pratas?size=20&sort=name,asc**

Fazendo sorting por ordem alfabética:

```json
 "content": [
        {
            "id": 11,
            "name": "ae"
        },
        {
            "id": 12,
            "name": "Eduardo"
        },
        {
            "id": 16,
            "name": "Prata1"
        }
 ]
```

**http://localhost:8080/pratas?size=20&sort=name,desc**
**http://localhost:8080/pratas?size=20&sort=id,desc**

Verificando no console vemos que estamos adicionando diretamente o valor que esta sendo retornado do banco de dados da nossa query:

O Hibernate esta trazendo o sql fora do padrão quando é um spring, isso acontece porque quando utiliza o `show-sql: true` ele simplesmente vai imprimir diretamente no console sem utilizar nenhum tipo de appender, que no caso o spring utiliza `logback`, é bom alterar por relação de performance pode usar o ``loggin: level`, não para toda aplicação pode fazer isso direto para um pacote`:

```bash
2025-01-17T12:45:52.583-03:00  INFO 24608 --- [nio-8080-exec-8] b.c.p.controller.PrataController         : 2025-01-17 12:45:52
Hibernate: select p1_0.id,p1_0.name from prata p1_0 order by p1_0.id desc limit ?,?

```

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

```bash
Hibernate: select p1_0.id,p1_0.name from prata p1_0 order by p1_0.id desc limit ?,?
```

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

logging:
  level:
    org:
      hibernate:
        SQL: DEBUG
```

```bash
2025-01-17T13:04:15.914-03:00 DEBUG 15356 --- [nio-8080-exec-1] org.hibernate.SQL                        : select p1_0.id,p1_0.name from prata p1_0 order by p1_0.id desc limit ?,?
```
