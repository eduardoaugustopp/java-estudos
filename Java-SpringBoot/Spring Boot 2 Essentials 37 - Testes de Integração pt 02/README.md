## Spring Boot 2 Essentials 37 - Testes de Integração pt 02

O teste esta listando o log em modo debbug e acab tendo um tempo consideravel na hora de executar os testes;

Dentro da pasta teste cria um novo diretório `resources` e um arquivo `logback-test.xml`;

**https://www.baeldung.com/logback**

```xml
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>

  <root level="debug">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

```java
package br.com.pkpratas01.integration; // Define o pacote do arquivo, para organizar o código.

import java.util.List; // Importa a classe List para usar listas de objetos.

import org.assertj.core.api.Assertions; // Biblioteca para facilitar a escrita de testes com fluência.
import org.junit.jupiter.api.DisplayName; // Permite adicionar um nome legível aos testes.
import org.junit.jupiter.api.Test; // Anotação que marca os métodos como testes.
import org.springframework.beans.factory.annotation.Autowired; // Anotação para injetar dependências automaticamente.
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase; // Configuração automática do banco de dados.
import org.springframework.boot.test.context.SpringBootTest; // Configuração do Spring para testes de integração.
import org.springframework.boot.test.web.client.TestRestTemplate; // Classe para testar requisições HTTP.
import org.springframework.boot.test.web.server.LocalServerPort; // Obtém a porta onde o servidor está rodando.
import org.springframework.core.ParameterizedTypeReference; // Utilizado para gerar um tipo parametrizado (como uma lista).
import org.springframework.http.HttpEntity; // Representa a requisição HTTP.
import org.springframework.http.HttpMethod; // Representa o método HTTP (GET, POST, etc).
import org.springframework.http.HttpStatus; // Representa o código de status HTTP.
import org.springframework.http.ResponseEntity; // Representa a resposta HTTP.
import org.springframework.test.annotation.DirtiesContext; // Limpa o contexto entre os testes.

import br.com.pkpratas01.domain.Prata; // Importa a entidade 'Prata' (modelo de dados).
import br.com.pkpratas01.repository.PrataRepository; // Importa o repositório para acessar dados da entidade 'Prata'.
import br.com.pkpratas01.request.PrataPostRequestBody; // Importa o modelo de requisição para criação de uma 'Prata'.
import br.com.pkpratas01.util.PrataCreator; // Utilitário para criar objetos 'Prata' para testes.
import br.com.pkpratas01.util.PrataPostRequestBodyCreator; // Utilitário para criar objetos 'PrataPostRequestBody'.
import br.com.pkpratas01.wrapper.PageableResponse; // Utilitário que encapsula resposta paginada.

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT) // Inicializa o Spring Boot em um servidor web com uma porta aleatória para testes.
@AutoConfigureTestDatabase // Configura o banco de dados automaticamente para testes.
@DirtiesContext(classMode = DirtiesContext.ClassMode.BEFORE_EACH_TEST_METHOD) // Limpa o contexto antes de cada método de teste para garantir que os dados do banco de dados não interfiram entre os testes.
public class PrataControllerIT { // Define uma classe de testes de integração para o controlador 'PrataController'.

    @Autowired
    private TestRestTemplate testRestTemplate; // Injeta a dependência de TestRestTemplate para simular requisições HTTP durante os testes.

    @LocalServerPort
    private int port; // Obtém a porta onde o servidor está rodando, para montar URLs dinamicamente.

    @Autowired
    private PrataRepository prataRepository; // Injeta o repositório para interagir com o banco de dados.

    // Teste para verificar se o método 'list' retorna uma lista de objetos 'Prata' com sucesso.
    @Test
    @DisplayName("list returns list of prata inside page object when successful")
    void list_ReturnsListOfPratasInsidePageObject_WhenSuccessful() {
        // Cria e salva uma 'Prata' no banco de dados.
        Prata savedPrata = prataRepository.save(PrataCreator.createPrataToBeSaved());

        // Obtém o nome da 'Prata' salva para comparar no teste.
        String expectedName = savedPrata.getName();

        // Faz uma requisição GET para o endpoint "/pratas" e mapeia a resposta para um objeto 'PageableResponse<Prata>'.
        PageableResponse<Prata> prataPage = testRestTemplate.exchange("/pratas", HttpMethod.GET, null,
                new ParameterizedTypeReference<PageableResponse<Prata>>() {
                }).getBody();

        // Verifica se a resposta não é nula.
        Assertions.assertThat(prataPage).isNotNull();

        // Verifica se a lista de 'Prata' dentro da página não está vazia e tem exatamente 1 item.
        Assertions.assertThat(prataPage.toList())
                .isNotEmpty()
                .hasSize(1);

        // Verifica se o nome da 'Prata' retornada é igual ao esperado.
        Assertions.assertThat(prataPage.toList().get(0).getName()).isEqualTo(expectedName);
    }

    // Teste para verificar se o método 'listAll' retorna uma lista de 'Prata' corretamente.
    @Test
    @DisplayName("listAll returns list of prata when successful")
    void listAll_ReturnsListOfPratas_WhenSuccessful() {
        // Cria e salva uma 'Prata' no banco de dados.
        Prata savedPrata = prataRepository.save(PrataCreator.createPrataToBeSaved());

        // Obtém o nome da 'Prata' salva para comparação.
        String expectedName = savedPrata.getName();

        // Faz uma requisição GET para o endpoint "/pratas/all" e mapeia a resposta para uma lista de 'Prata'.
        List<Prata> pratas = testRestTemplate.exchange("/pratas/all", HttpMethod.GET, null,
                new ParameterizedTypeReference<List<Prata>>() {
                }).getBody();

        // Verifica se a lista não é nula, não está vazia e tem exatamente 1 item.
        Assertions.assertThat(pratas)
                .isNotNull()
                .isNotEmpty()
                .hasSize(1);

        // Verifica se o nome da 'Prata' na lista retornada é o esperado.
        Assertions.assertThat(pratas.get(0).getName()).isEqualTo(expectedName);
    }

    // Teste para verificar se o método 'findById' retorna uma 'Prata' quando encontrado.
    @Test
    @DisplayName("findById returns prata when successful")
    void findById_ReturnsPrata_WhenSuccessful() {
        // Cria e salva uma 'Prata' no banco de dados.
        Prata savedPrata = prataRepository.save(PrataCreator.createPrataToBeSaved());

        // Obtém o ID da 'Prata' salva.
        Long expectedId = savedPrata.getId();

        // Faz uma requisição GET para o endpoint "/pratas/{id}" passando o ID da 'Prata'.
        Prata prata = testRestTemplate.getForObject("/pratas/{id}", Prata.class, expectedId);

        // Verifica se a 'Prata' retornada não é nula e se o ID é igual ao esperado.
        Assertions.assertThat(prata).isNotNull();
        Assertions.assertThat(prata.getId()).isNotNull().isEqualTo(expectedId);
    }

    // Teste para verificar se o método 'findByName' retorna uma lista de 'Prata' quando encontrado.
    @Test
    @DisplayName("findByName returns a list of prata when successful")
    void findByName_ReturnsListOfPrata_WhenSuccessful() {
        // Cria e salva uma 'Prata' no banco de dados.
        Prata savedPrata = prataRepository.save(PrataCreator.createPrataToBeSaved());

        // Obtém o nome da 'Prata' salva.
        String expectedName = savedPrata.getName();

        // Cria a URL para a busca com o nome da 'Prata'.
        String url = String.format("/pratas/find?name=%s", expectedName);

        // Faz uma requisição GET para o endpoint "/pratas/find" com o nome da 'Prata'.
        List<Prata> pratas = testRestTemplate.exchange(url, HttpMethod.GET, null,
                new ParameterizedTypeReference<List<Prata>>() {
                }).getBody();

        // Verifica se a lista retornada não é nula, não está vazia e contém a 'Prata' esperada.
        Assertions.assertThat(pratas)
                .isNotNull()
                .isNotEmpty()
                .hasSize(1);

        // Verifica se o nome da 'Prata' retornada é igual ao esperado.
        Assertions.assertThat(pratas.get(0).getName()).isEqualTo(expectedName);
    }

    // Teste para verificar o comportamento quando o nome da 'Prata' não é encontrado.
    @Test
    @DisplayName("findByName returns an empty list of prata when prata is not found")
    void findByName_ReturnsEmptyListOfPrata_WhenPrataIsNotFound() {
        // Faz uma requisição GET para buscar uma 'Prata' com o nome 'dbz' (que não existe).
        List<Prata> pratas = testRestTemplate.exchange("/pratas/find?name=dbz", HttpMethod.GET, null,
                new ParameterizedTypeReference<List<Prata>>() {
                }).getBody();

        // Verifica se a lista retornada é vazia.
        Assertions.assertThat(pratas)
                .isNotNull()
                .isEmpty();
    }

    // Teste para verificar se o método 'save' salva uma nova 'Prata' com sucesso.
    @Test
    @DisplayName("save returns prata when successful")
    void save_ReturnsPrata_WhenSuccessful() {
        // Cria um objeto de requisição 'PrataPostRequestBody' com os dados da 'Prata'.
        PrataPostRequestBody prataPostRequestBody = PrataPostRequestBodyCreator.createPrataPostRequestBody();

        // Faz uma requisição POST para o endpoint "/pratas" para salvar a 'Prata'.
        ResponseEntity<Prata> prataResponseEntity = testRestTemplate.postForEntity("/pratas", prataPostRequestBody,
                Prata.class);

        // Verifica se a resposta não é nula e se o status HTTP é 'CREATED' (201).
        Assertions.assertThat(prataResponseEntity).isNotNull();
        Assertions.assertThat(prataResponseEntity.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        Assertions.assertThat(prataResponseEntity.getBody()).isNotNull();
        Assertions.assertThat(prataResponseEntity.getBody().getId()).isNotNull();
    }

    // Teste para verificar se o método 'replace' atualiza uma 'Prata' com sucesso.
    @Test
    @DisplayName("replace updates prata when successful")
    void replace_UpdatesPrata_WhenSuccessful() {
        // Cria e salva uma 'Prata' no banco de dados.
        Prata savedPrata = prataRepository.save(PrataCreator.createPrataToBeSaved());

        // Modifica o nome da 'Prata' salva.
        savedPrata.setName("new name");

        // Faz uma requisição PUT para atualizar a 'Prata'.
        ResponseEntity<Void> prataResponseEntity = testRestTemplate.exchange("/pratas",
                HttpMethod.PUT, new HttpEntity<>(savedPrata), Void.class);

        // Verifica se a resposta é válida e o status é 'NO_CONTENT' (204).
        Assertions.assertThat(prataResponseEntity).isNotNull();
        Assertions.assertThat(prataResponseEntity.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT);
    }

    // Teste para verificar se o método 'delete' remove uma 'Prata' com sucesso.
    @Test
    @DisplayName("delete removes prata when successful")
    void delete_RemovesPrata_WhenSuccessful() {
        // Cria e salva uma 'Prata' no banco de dados.
        Prata savedPrata = prataRepository.save(PrataCreator.createPrataToBeSaved());

        // Faz uma requisição DELETE para remover a 'Prata' com o ID correspondente.
        ResponseEntity<Void> prataResponseEntity = testRestTemplate.exchange("/pratas/{id}",
                HttpMethod.DELETE, null, Void.class, savedPrata.getId());

        // Verifica se a resposta é válida e o status é 'NO_CONTENT' (204).
        Assertions.assertThat(prataResponseEntity).isNotNull();
        Assertions.assertThat(prataResponseEntity.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT);
    }
}
```
