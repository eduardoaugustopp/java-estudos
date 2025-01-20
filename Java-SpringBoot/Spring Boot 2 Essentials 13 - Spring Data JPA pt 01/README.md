## Spring Boot 2 Essentials 13 - Spring Data JPA pt 01

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
    <scope>runtime</scope>
</dependency>
```

`apllication.yml`

**useSSL=false** - false porque estamos trabalhando localmente;

`**jpa:
    hibernate:
      ddl-auto: update**` - configuração para que todas as vezes que iniciarmos nosso banco, ele vai atualizar o banco;

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3307/prata?useSSL=false&createDatabaseIfNotExist=true&allowPublicKeyRetrieval=true
    username: root
    password: root
  jpa:
    hibernate:
      ddl-auto: update
```

Agora precisa transformar nosso `dominio` que é Prata `em entidade`:

```java
// Importa as anotações do Lombok e JPA/Hibernate necessárias para o funcionamento da classe
import lombok.Data;
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;
import jakarta.persistence.*;

// Lombok: Gera automaticamente os métodos getters, setters, toString, equals e hashCode
@Data
// Lombok: Gera um construtor com todos os atributos da classe como parâmetros
@AllArgsConstructor
// Lombok: Gera um construtor vazio (sem parâmetros)
@NoArgsConstructor
// JPA: Indica que essa classe é uma entidade que será mapeada para uma tabela no banco de dados
@Entity
public class Prata {
    // JPA: Indica que esse campo é a chave primária da tabela
    @Id
    // JPA: Define que o valor do ID será gerado automaticamente pelo banco de dados,
    // utilizando a estratégia "IDENTITY" (auto-incremento no banco)
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // Esse campo será uma coluna no banco de dados para armazenar o nome da "Prata"
    private String name;
}

```
