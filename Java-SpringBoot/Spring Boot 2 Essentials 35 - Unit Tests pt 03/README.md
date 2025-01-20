## Spring Boot 2 Essentials 35 - Unit Tests pt 03

Criar os testes para os serviços, pacote `service`;

```java
package br.com.pkpratas01.controller;

import java.util.Collections;
import java.util.List;

import org.assertj.core.api.Assertions; // Biblioteca AssertJ para validações em testes
import org.junit.jupiter.api.BeforeEach; // Executa o código antes de cada teste
import org.junit.jupiter.api.DisplayName; // Define o nome descritivo para os testes
import org.junit.jupiter.api.Test; // Define os métodos de teste
import org.junit.jupiter.api.extension.ExtendWith; // Permite usar extensões no JUnit 5
import org.mockito.ArgumentMatchers; // Ajuda a capturar argumentos em métodos mockados
import org.mockito.BDDMockito; // Fornece métodos para simular o comportamento de mocks
import org.mockito.InjectMocks; // Injeta os mocks nos objetos necessários
import org.mockito.Mock; // Cria mocks para os objetos
import org.springframework.data.domain.Page; // Representa uma página de dados
import org.springframework.data.domain.PageImpl; // Implementação de uma página
import org.springframework.http.HttpStatus; // Representa status HTTP
import org.springframework.http.ResponseEntity; // Representa a resposta HTTP
import org.springframework.test.context.junit.jupiter.SpringExtension; // Integração com o Spring no JUnit 5

// Importações de classes do projeto
import br.com.pkpratas01.domain.Prata;
import br.com.pkpratas01.request.PrataPostRequestBody;
import br.com.pkpratas01.request.PrataPutRequestBody;
import br.com.pkpratas01.service.PrataService;
import br.com.pkpratas01.util.PrataCreator;
import br.com.pkpratas01.util.PrataPostRequestBodyCreator;
import br.com.pkpratas01.util.PrataPutRequestBodyCreator;

// Configura a classe para usar a extensão do Spring no JUnit
@ExtendWith(SpringExtension.class)
public class PrataControllerTest {

    // Injeta a classe PrataController e substitui suas dependências pelos mocks
    @InjectMocks
    private PrataController prataController;

    // Cria um mock do serviço PrataService
    @Mock
    private PrataService prataServiceMock;

    // Este método é executado antes de cada teste para configurar os mocks
    @BeforeEach
    void setUp() {
        // Simula o retorno de um objeto Page contendo uma lista de "Prata"
        PageImpl<Prata> prataPage = new PageImpl<>(List.of(PrataCreator.createValidPrata()));
        BDDMockito.when(prataServiceMock.listAll(ArgumentMatchers.any()))
                .thenReturn(prataPage);

        // Simula o retorno de uma lista de "Prata"
        BDDMockito.when(prataServiceMock.listAllNonPageable())
                .thenReturn(List.of(PrataCreator.createValidPrata()));

        // Simula o retorno de uma "Prata" específica ao buscar por ID
        BDDMockito.when(prataServiceMock.findByIdOrThrowBadRequestException(ArgumentMatchers.anyLong()))
                .thenReturn(PrataCreator.createValidPrata());

        // Simula o retorno de uma lista de "Prata" ao buscar por nome
        BDDMockito.when(prataServiceMock.findByName(ArgumentMatchers.anyString()))
                .thenReturn(List.of(PrataCreator.createValidPrata()));

        // Simula o salvamento de uma "Prata"
        BDDMockito.when(prataServiceMock.save(ArgumentMatchers.any(PrataPostRequestBody.class)))
                .thenReturn(PrataCreator.createValidPrata());

        // Simula o comportamento do método replace sem lançar exceções
        BDDMockito.doNothing().when(prataServiceMock).replace(ArgumentMatchers.any(PrataPutRequestBody.class));

        // Simula o comportamento do método delete sem lançar exceções
        BDDMockito.doNothing().when(prataServiceMock).delete(ArgumentMatchers.anyLong());
    }

    @Test
    @DisplayName("list returns list of prata inside page object when successful")
    void list_ReturnsListOfPratasInsidePageObject_WhenSuccessful() {
        // Testa o método list e verifica se retorna uma página com os dados esperados
        String expectedName = PrataCreator.createValidPrata().getName();

        Page<Prata> prataPage = prataController.list(null).getBody();

        Assertions.assertThat(prataPage).isNotNull();

        Assertions.assertThat(prataPage.toList())
                .isNotEmpty()
                .hasSize(1);

        Assertions.assertThat(prataPage.toList().get(0).getName()).isEqualTo(expectedName);
    }

    @Test
    @DisplayName("listAll returns list of prata when successful")
    void listAll_ReturnsListOfPratas_WhenSuccessful() {
        // Testa se o método listAll retorna uma lista não vazia
        String expectedName = PrataCreator.createValidPrata().getName();

        List<Prata> pratas = prataController.listAll().getBody();

        Assertions.assertThat(pratas)
                .isNotNull()
                .isNotEmpty()
                .hasSize(1);

        Assertions.assertThat(pratas.get(0).getName()).isEqualTo(expectedName);
    }

    @Test
    @DisplayName("findById returns prata when successful")
    void findById_ReturnsPrata_WhenSuccessful() {
        // Testa se o método findById retorna o objeto correto
        Long expectedId = PrataCreator.createValidPrata().getId();

        Prata prata = prataController.findById(1).getBody();

        Assertions.assertThat(prata).isNotNull();
        Assertions.assertThat(prata.getId()).isNotNull().isEqualTo(expectedId);
    }

    @Test
    @DisplayName("findByName returns a list of prata when successful")
    void findByName_ReturnsListOfPrata_WhenSuccessful() {
        // Testa se o método findByName retorna os objetos esperados
        String expectedName = PrataCreator.createValidPrata().getName();

        List<Prata> pratas = prataController.findByName("prata").getBody();

        Assertions.assertThat(pratas)
                .isNotNull()
                .isNotEmpty()
                .hasSize(1);

        Assertions.assertThat(pratas.get(0).getName()).isEqualTo(expectedName);
    }

    @Test
    @DisplayName("findByName returns an empty list of prata when prata is not found")
    void findByName_ReturnsEmptyListOfPrata_WhenPrataIsNotFound() {
        // Simula o caso onde nenhuma "Prata" é encontrada pelo nome
        BDDMockito.when(prataServiceMock.findByName(ArgumentMatchers.anyString()))
                .thenReturn(Collections.emptyList());

        List<Prata> pratas = prataController.findByName("prata").getBody();

        Assertions.assertThat(pratas)
                .isNotNull()
                .isEmpty();
    }

    @Test
    @DisplayName("save returns prata when successful")
    void save_ReturnsPrata_WhenSuccessful() {
        // Testa se o método save salva e retorna a "Prata" esperada
        Prata prata = prataController.save(PrataPostRequestBodyCreator.createPrataPostRequestBody()).getBody();

        Assertions.assertThat(prata).isNotNull().isEqualTo(PrataCreator.createValidPrata());
    }

    @Test
    @DisplayName("replace updates prata when successful")
    void replace_UpdatesPrata_WhenSuccessful() {
        // Testa se o método replace não lança exceções e retorna o status correto
        Assertions.assertThatCode(() -> prataController.replace(PrataPutRequestBodyCreator.createPrataPutRequestBody()))
                .doesNotThrowAnyException();

        ResponseEntity<Void> entity = prataController.replace(PrataPutRequestBodyCreator.createPrataPutRequestBody());

        Assertions.assertThat(entity).isNotNull();
        Assertions.assertThat(entity.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT);
    }

    @Test
    @DisplayName("delete removes prata when successful")
    void delete_RemovesPrata_WhenSuccessful() {
        // Testa se o método delete não lança exceções e retorna o status correto
        Assertions.assertThatCode(() -> prataController.delete(1))
                .doesNotThrowAnyException();

        ResponseEntity<Void> entity = prataController.delete(1);

        Assertions.assertThat(entity).isNotNull();
        Assertions.assertThat(entity.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT);
    }
}
```
