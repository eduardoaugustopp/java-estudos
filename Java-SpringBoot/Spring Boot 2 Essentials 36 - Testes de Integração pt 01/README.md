## Spring Boot 2 Essentials 36 - Testes de Integração pt 01

É rodar a integração inteira o mais próximo possível do ambiente, pacote `integration`;

```java
package br.com.pkpratas01.integration;

// Importações necessárias para o teste, como bibliotecas de teste e classes do Spring Boot
import org.assertj.core.api.Assertions; // Biblioteca AssertJ para validações e assertivas
import org.junit.jupiter.api.DisplayName; // Para dar nomes descritivos aos testes
import org.junit.jupiter.api.Test; // Para marcar métodos como testes
import org.springframework.beans.factory.annotation.Autowired; // Para injeção de dependência
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase; // Configuração para o banco de dados em testes
import org.springframework.boot.test.context.SpringBootTest; // Anotação para configurar um teste de integração do Spring Boot
import org.springframework.boot.test.web.client.TestRestTemplate; // Cliente para realizar requisições HTTP nos testes
import org.springframework.boot.test.web.server.LocalServerPort; // Injeta a porta do servidor local
import org.springframework.core.ParameterizedTypeReference; // Permite lidar com tipos genéricos em respostas de requisições HTTP
import org.springframework.http.HttpMethod; // Enum para definir métodos HTTP

import br.com.pkpratas01.domain.Prata; // Importa a entidade "Prata"
import br.com.pkpratas01.repository.PrataRepository; // Importa o repositório para interação com o banco de dados
import br.com.pkpratas01.util.PrataCreator; // Classe utilitária para criar objetos "Prata" para os testes
import br.com.pkpratas01.wrapper.PageableResponse; // Classe para lidar com respostas paginadas

// Indica que este é um teste de integração do Spring Boot com um servidor de porta aleatória
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
// Configura o teste para usar um banco de dados em memória, evitando alterações no banco de dados real
@AutoConfigureTestDatabase
public class PrataControllerIT {
    // Injeta uma instância do TestRestTemplate para realizar requisições HTTP nos testes
    @Autowired
    private TestRestTemplate testRestTemplate;

    // Injeta o número da porta do servidor local
    @LocalServerPort
    private int port;

    // Injeta o repositório de "Prata" para interagir com o banco de dados
    @Autowired
    private PrataRepository prataRepository;

    // Define um teste
    @Test
    @DisplayName("list returns list of prata inside page object when successful") // Nome descritivo para o teste
    void list_ReturnsListOfPratasInsidePageObject_WhenSuccessful() {
        // Salva uma instância de "Prata" no banco de dados antes do teste
        Prata savedPrata = prataRepository.save(PrataCreator.createPrataToBeSaved());

        // Obtém o nome esperado da instância salva
        String expectedName = savedPrata.getName();

        // Faz uma requisição GET para o endpoint "/pratas"
        PageableResponse<Prata> prataPage = testRestTemplate.exchange(
                "/pratas", // URL do endpoint
                HttpMethod.GET, // Método HTTP
                null, // Sem corpo na requisição
                new ParameterizedTypeReference<PageableResponse<Prata>>() {
                } // Tipo esperado na resposta
        ).getBody(); // Obtém o corpo da resposta

        // Verifica se a resposta não é nula
        Assertions.assertThat(prataPage).isNotNull();

        // Verifica se a lista dentro do objeto página não está vazia e contém apenas um item
        Assertions.assertThat(prataPage.toList())
                .isNotEmpty() // Não está vazia
                .hasSize(1); // Contém exatamente um item

        // Verifica se o nome do primeiro item na lista é igual ao nome esperado
        Assertions.assertThat(prataPage.toList().get(0).getName()).isEqualTo(expectedName);
    }
}
```

Pacote `wrapper`:

```java
package br.com.pkpratas01.wrapper;

// Importa as anotações do Jackson para serializar e desserializar objetos JSON
import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonCreator.Mode;
import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.databind.JsonNode;

// Importa classes para trabalhar com listas e paginação
import java.util.List;
import lombok.Getter;
import lombok.Setter;

// Importa classes do Spring Data para facilitar o trabalho com dados paginados
import org.springframework.data.domain.PageImpl;
import org.springframework.data.domain.PageRequest;

// As anotações @Getter e @Setter do Lombok geram automaticamente os métodos "get" e "set" para os atributos desta classe.
@Getter
@Setter
public class PageableResponse<T> extends PageImpl<T> {
    // Atributos adicionais para controlar se é a primeira ou última página,
    // o total de páginas e o número de elementos na página atual.
    private boolean first;
    private boolean last;
    private int totalPages;
    private int numberOfElements;

    /**
     * Construtor personalizado para a classe. Ele usa a anotação @JsonCreator,
     * que permite que o Jackson (biblioteca para JSON) crie objetos dessa classe
     * a partir de uma representação JSON.
     *
     * @param content          Lista de elementos que compõem a página atual.
     * @param number           Número da página atual (índice começa em 0).
     * @param size             Tamanho da página (quantidade de elementos por página).
     * @param totalElements    Total de elementos disponíveis em todas as páginas.
     * @param last             Indica se esta é a última página.
     * @param first            Indica se esta é a primeira página.
     * @param totalPages       Número total de páginas disponíveis.
     * @param numberOfElements Quantidade de elementos na página atual.
     * @param pageable         Representa as informações sobre a paginação (não usado diretamente aqui).
     * @param sort             Representa as informações sobre ordenação (não usado diretamente aqui).
     */
    @JsonCreator(mode = Mode.PROPERTIES)
    public PageableResponse(@JsonProperty("content") List<T> content,
            @JsonProperty("number") int number,
            @JsonProperty("size") int size,
            @JsonProperty("totalElements") int totalElements,
            @JsonProperty("last") boolean last,
            @JsonProperty("first") boolean first,
            @JsonProperty("totalPages") int totalPages,
            @JsonProperty("numberOfElements") int numberOfElements,
            @JsonProperty("pageable") JsonNode pageable, // Objeto JSON contendo dados da paginação (não usado diretamente).
            @JsonProperty("sort") JsonNode sort) {       // Objeto JSON contendo dados da ordenação (não usado diretamente).
        // Chama o construtor da classe pai (PageImpl) passando o conteúdo, a página atual e o total de elementos.
        super(content, PageRequest.of(number, size), totalElements);

        // Inicializa os atributos adicionais desta classe.
        this.last = last;
        this.first = first;
        this.totalPages = totalPages;
        this.numberOfElements = numberOfElements;
    }
}
```
