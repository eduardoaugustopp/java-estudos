## Spring Boot 2 Essentials 26 - RestTemplate getForObject e getForEntity

Quando esta desenvolvendo as suas aplicações em algum momento você vai precisar fazer a requisição para outros serviços para alguma URL externa e você não quer fazer essa requisição manualmente, o spring oferece uma ferramenta uma biblioteca que vai fazer essa chamada, vai fazer automaticamente o mapeamento dos dados para sua classe;

Cria um pacote `client` e cria uma classe `SpringClient`;

Vamos imaginar que você quer fazer uma requisição para o seu próprio serviço, você sabe que esta rodando na porta 8080:

```java
package br.com.pkpratas01.client;

// Importações necessárias
import org.springframework.http.ResponseEntity; // Para representar a resposta HTTP completa
import org.springframework.web.client.RestTemplate; // Para realizar chamadas HTTP
import br.com.pkpratas01.domain.Prata; // Classe que representa o objeto "Prata" (deve estar no seu domínio)
import lombok.extern.log4j.Log4j2; // Para criar um logger usando Log4j2

@Log4j2 // Habilita o uso do log com a anotação @Log4j2
public class SpringClient {
    public static void main(String[] args) {
        // Faz uma requisição HTTP GET para a URL especificada com o método getForEntity.
        // O resultado é um ResponseEntity que contém o objeto "Prata" e metadados da resposta.
        ResponseEntity<Prata> entity = new RestTemplate()
                .getForEntity("http://localhost:8080/pratas/{id}", Prata.class, 2); // "{id}" será substituído por 2, se tiver mais de um placeholder é só colocar o valor seperado por ','

        // Loga a resposta completa (incluindo cabeçalhos e status HTTP)
        log.info(entity);

        // Faz outra requisição HTTP GET para a mesma URL, mas com o método getForObject.
        // Este método retorna diretamente o objeto "Prata", sem os metadados da resposta.
        Prata object = new RestTemplate()
                .getForObject("http://localhost:8080/pratas/{id}", Prata.class, 2);

        // Loga apenas o objeto retornado
        log.info(object);
    }
}
```
