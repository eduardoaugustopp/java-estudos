## Spring Boot 2 Essentials 17 - Exceções Customizadas

Cria pacote `exception`;

```java
package br.com.pkpratas01.exception;

// Importa a classe HttpStatus para usar os códigos de status HTTP.
import org.springframework.http.HttpStatus;
// Importa a anotação @ResponseStatus, que permite associar a exceção a um código de status HTTP.
import org.springframework.web.bind.annotation.ResponseStatus;

// Anotação para indicar que, quando essa exceção for lançada, o código HTTP retornado será 400 (BAD REQUEST).
@ResponseStatus(HttpStatus.BAD_REQUEST)
public class BadRequestException extends RuntimeException { // Define uma exceção personalizada que herda de RuntimeException.

    // Construtor da exceção que recebe uma mensagem como argumento.
    public BadRequestException(String message) {
        // Chama o construtor da classe pai (RuntimeException), passando a mensagem recebida.
        super(message);
    }
}
```

Pacote service:

```java
    public Prata findByIdOrThrowBadRequestException(long id) {
        return prataRepository.findById(id)
                .orElseThrow(() -> new BadRequestException("Prata not Found"));
    }
```
