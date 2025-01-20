## Spring Boot 2 Essentials 18 - Handler Global

GET http://localhost:8080/pratas/1

`400 Bad Request`

```json
{
  "timestamp": "2025-01-16T19:47:03.707+00:00",
  "status": 400,
  "error": "Bad Request",
  "message": "Prata not Found",
  "path": "/pratas/1"
}
```

Cria uma classe `BadRequestExceptionDetails`;

```java
package br.com.pkpratas01.exception;

import java.time.LocalDateTime;

import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class BadRequestExceptionDetails {
    private String title;
    private int status;
    private String details;
    private String developerMessage;
    private LocalDateTime timestamp;
}
```

Agora que temos essa classe precisamos falar para todos os controllers que quando tiver uma exceção do tipo `BadRequestException` precisa utilizar o método que vamos criar;

Cria um pacote `handler` e a classe `RestExceptionHandler.java`;

```java
// Indica que esta classe faz parte do pacote 'br.com.pkpratas01.handler'.
package br.com.pkpratas01.handler;

// Importa classes necessárias para trabalhar com datas e horas.
import java.time.LocalDateTime;

// Importa classes para definir o status HTTP e criar respostas HTTP.
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

// Importa classes para criar controladores de exceções.
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

// Importa as exceções personalizadas usadas no sistema.
import br.com.pkpratas01.exception.BadRequestException;
import br.com.pkpratas01.exception.BadRequestExceptionDetails;

// Marca esta classe como um "Advice" para controladores.
// Isso significa que ela "aconselha" controladores, permitindo lidar com exceções de maneira centralizada.
@ControllerAdvice
public class RestExceptionHandler {

    // Método para tratar exceções do tipo 'BadRequestException'.
    // O @ExceptionHandler informa ao Spring que este método é responsável por lidar com essa exceção específica.
    @ExceptionHandler(BadRequestException.class)
    public ResponseEntity<BadRequestExceptionDetails> handlerBadRequestException(BadRequestException bre) {
        // Cria e retorna uma resposta HTTP contendo os detalhes da exceção.
        // Utiliza o objeto 'BadRequestExceptionDetails' para estruturar as informações.
        return new ResponseEntity<>(
                // Utiliza o padrão Builder para criar um objeto 'BadRequestExceptionDetails'.
                BadRequestExceptionDetails.builder()
                        .timestamp(LocalDateTime.now()) // Adiciona o timestamp atual (quando a exceção ocorreu).
                        .status(HttpStatus.BAD_REQUEST.value()) // Define o status HTTP como 400 (BAD_REQUEST).
                        .title("Bad Request Exception, Check the Documentation") // Título da exceção.
                        .details(bre.getMessage()) // Mensagem detalhada da exceção (vem de 'BadRequestException').
                        .developerMessage(bre.getClass().getName()) // Informação adicional para o desenvolvedor (nome da classe da exceção).
                        .build(), // Constrói o objeto 'BadRequestExceptionDetails'.
                HttpStatus.BAD_REQUEST // Define o status HTTP na resposta.
        );
    }
}
```

**http://localhost:8080/pratas/1**

```json
{
  "title": "Bad Request Exception, Check the Documentation",
  "status": 400,
  "details": "Prata not Found",
  "developerMessage": "br.com.pkpratas01.exception.BadRequestException",
  "timestamp": "2025-01-16T17:00:39.8778898"
}
```
