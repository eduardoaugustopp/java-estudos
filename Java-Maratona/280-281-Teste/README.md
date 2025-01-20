## Testes unitários com jUnit pt 01

Quando esta trabalhando com desenvolvimento de software precisa fazer manutenção em códigos, vai ter pessoas trabalhando no mesmo repositório e precisa manter o comportamento e é pra isso tem os teste unitários, tem vários tipos de testes - testes unitários, teste de integração, teste de componente, teste de comportamento;

Teste unitário é o mais básico, ele testa uma unidade do seu código, geralmente um método;

```java
package academy.devdojo.maratonajava.javacore.ZZKjunit.dominio;

import lombok.AllArgsConstructor;
import lombok.Data;

@Data
@AllArgsConstructor
public class Person {
    private int age;
}
```

**`Desenvolver a lógica de negócio - lógica de negócio é criar um método aonde a pessoa é adulta ou não, vai retornar um booleano baseado na idade`**

```java
package academy.devdojo.maratonajava.javacore.ZZKjunit.service;

import academy.devdojo.maratonajava.javacore.ZZKjunit.dominio.Person;

import java.util.Objects;

public class PersonService {
    public boolean isAdult(Person person){
        Objects.requireNonNull(person, "Person can't be null");//temos validação
        return person.getAge() >= 18;
    }
}
```

Dessa maneira é como estavamos fazendo para testar mas como saber o funcionamento após algum tempo ?

```java
package academy.devdojo.maratonajava.javacore.ZZKjunit.test;

import academy.devdojo.maratonajava.javacore.ZZKjunit.dominio.Person;
import academy.devdojo.maratonajava.javacore.ZZKjunit.service.PersonService;
import lombok.extern.log4j.Log4j2;

@Log4j2
public class PersonServiceTest01 {
    public static void main(String[] args) {
        Person person = new Person(15);
        PersonService personService = new PersonService();
        log.info("Is Adult? '{}'", personService.isAdult(person));

    }
}
```

**Para ter garantia que uma mudança não vai afetar outros códigos é para isso que existe os `testes unitários`;**

A primeira coisa é que precisa de uma biblioteca e a mais conhecida é a **`JUNIT`;**

\*\* Quando cria projeto spring boot já vem automatico

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.11.4</version>
    <scope>test</scope>
</dependency>
```

`Assertivos` - tem que ser assim.

Precisa colocar um nome no teste - `nome do método` `o que ele tem que fazer` e a `condição` ou usar anotação `@DisplayName`;

```java
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

class PersonServiceTest {

    @Test
    @DisplayName("A person should be not adult when age is lower than 18")
    void isAdult_ReturnFalse_WhenAgeIsLowerThan18() {
        // Corrigindo o construtor do Person
        Person person = new Person(15);
        PersonService personService = new PersonService();

        // Verificando a condição
        assertFalse(personService.isAdult(person));
    }
}

```
