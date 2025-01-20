## Spring Boot 2 Essentials 27 - RestTemplate exchange

Fazer isso para não ter que ficar manipulando os atributos que estão dentro de um objeto JSON nesse caso o `content`, manter as coisas mais simples:

**CONTROLLER**

```java
@GetMapping(path = "/all")
    public ResponseEntity<List<Prata>> listAll() {
        log.info(dateUtil.formatLocalDateTimeToDatabaseStyle(LocalDateTime.now()));
        return ResponseEntity.ok(prataService.listAllNonPageable());
    }
```

**SERVICE**

```java
public List<Prata> listAllNonPageable() {
    return prataRepository.findAll();
}
```

**CLIENT**

Imagina que você queira fazer o mapeamento automático dos dados porem agora os dados estão dentro de uma lista;

```java
package br.com.pkpratas01.client;

// Importações necessárias para trabalhar com tipos genéricos, respostas HTTP, e o cliente REST
import java.util.Arrays; // Para manipulação de arrays
import java.util.List; // Para trabalhar com listas

import org.springframework.core.ParameterizedTypeReference; // Para trabalhar com tipos genéricos em requisições
import org.springframework.http.HttpMethod; // Enum que define métodos HTTP como GET, POST, etc.
import org.springframework.http.ResponseEntity; // Representa uma resposta HTTP
import org.springframework.web.client.RestTemplate; // Cliente REST do Spring para consumo de APIs

import br.com.pkpratas01.domain.Prata; // Classe de domínio que representa o recurso "Prata"
import lombok.extern.log4j.Log4j2; // Usado para registrar logs com o framework Log4j2

@Log4j2 // Anotação do Lombok para habilitar logging usando Log4j2
public class SpringClient {
    public static void main(String[] args) {
        // Faz uma requisição GET para a URL especificada e retorna uma ResponseEntity<Prata>
        // "http://localhost:8080/pratas/{id}" é a URL, e "2" é o valor que substitui o {id} na URL
        ResponseEntity<Prata> entity = new RestTemplate().getForEntity("http://localhost:8080/pratas/{id}", Prata.class, 2);
        // Registra a resposta completa no log (inclui cabeçalhos, status, e o corpo da resposta)
        log.info(entity);

        // Faz outra requisição GET para a mesma URL, mas retorna apenas o corpo da resposta como um objeto Prata
        Prata object = new RestTemplate().getForObject("http://localhost:8080/pratas/{id}", Prata.class, 2);
        // Registra o objeto no log
        log.info(object);

        // Faz uma requisição GET para obter todos os objetos do tipo Prata como um array
        Prata[] pratas = new RestTemplate().getForObject("http://localhost:8080/pratas/all", Prata[].class);
        // Registra o array no log, convertendo-o para uma String para exibição
        log.info(Arrays.toString(pratas));

        // Utiliza o método exchange para fazer uma requisição GET mais flexível
        // O método exchange permite especificar o tipo genérico da resposta com ParameterizedTypeReference
        // "HttpMethod.GET" indica o método HTTP usado (GET), e "null" é o corpo da requisição (não necessário aqui)
        ResponseEntity<List<Prata>> exchange = new RestTemplate().exchange(
                "http://localhost:8080/pratas/all", // URL da API
                HttpMethod.GET,                    // Método HTTP
                null,                              // Corpo da requisição (não usado aqui)
                new ParameterizedTypeReference<>() {} // Define o tipo esperado da resposta (List<Prata>)
        );
        // Registra no log o corpo da resposta, que é uma lista de objetos Prata
        log.info(exchange.getBody());
    }
}
```
