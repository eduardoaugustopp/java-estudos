## Spring Boot 2 Essentials 34 - Unit Tests pt 02

Segundo método a testar é o list;

```java
package br.com.pkpratas01.controller;

// Importação de pacotes e bibliotecas necessários
import java.util.Collections;
import java.util.List;

import org.assertj.core.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.ArgumentMatchers;
import org.mockito.BDDMockito;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageImpl;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import br.com.pkpratas01.domain.Prata;
import br.com.pkpratas01.request.PrataPostRequestBody;
import br.com.pkpratas01.request.PrataPutRequestBody;
import br.com.pkpratas01.service.PrataService;
import br.com.pkpratas01.util.PrataCreator;
import br.com.pkpratas01.util.PrataPostRequestBodyCreator;
import br.com.pkpratas01.util.PrataPutRequestBodyCreator;

// Indica que o SpringExtension será usado para suportar as anotações @Mock e @InjectMocks
@ExtendWith(SpringExtension.class)
public class PrataControllerTest {

    // Cria uma instância mockada do PrataController para ser testada
    @InjectMocks
    private PrataController prataController;

    // Cria uma instância mockada do PrataService para simular comportamento
    @Mock
    private PrataService prataServiceMock;

    // Configura os mocks antes de cada teste
    @BeforeEach
    void setUp() {
        // Simula uma página contendo uma lista de objetos Prata
        PageImpl<Prata> prataPage = new PageImpl<>(List.of(PrataCreator.createValidPrata()));
        BDDMockito.when(prataServiceMock.listAll(ArgumentMatchers.any()))
                .thenReturn(prataPage); // Simula o retorno do método listAll com paginação

        BDDMockito.when(prataServiceMock.listAllNonPageable())
                .thenReturn(List.of(PrataCreator.createValidPrata())); // Simula o retorno de lista completa

        BDDMockito.when(prataServiceMock.findByIdOrThrowBadRequestException(ArgumentMatchers.anyLong()))
                .thenReturn(PrataCreator.createValidPrata()); // Simula busca por ID

        BDDMockito.when(prataServiceMock.findByName(ArgumentMatchers.anyString()))
                .thenReturn(List.of(PrataCreator.createValidPrata())); // Simula busca por nome

        BDDMockito.when(prataServiceMock.save(ArgumentMatchers.any(PrataPostRequestBody.class)))
                .thenReturn(PrataCreator.createValidPrata()); // Simula salvamento de uma Prata

        BDDMockito.doNothing().when(prataServiceMock).replace(ArgumentMatchers.any(PrataPutRequestBody.class));
        // Simula atualização de uma Prata

        BDDMockito.doNothing().when(prataServiceMock).delete(ArgumentMatchers.anyLong());
        // Simula exclusão de uma Prata
    }

    // Teste para verificar se a lista paginada é retornada corretamente
    @Test
    @DisplayName("list returns list of prata inside page object when successful")
    void list_ReturnsListOfPratasInsidePageObject_WhenSuccessful() {
        String expectedName = PrataCreator.createValidPrata().getName();

        // Invoca o método de listagem
        Page<Prata> prataPage = prataController.list(null).getBody();

        // Validações usando AssertJ
        Assertions.assertThat(prataPage).isNotNull();
        Assertions.assertThat(prataPage.toList())
                .isNotEmpty()
                .hasSize(1);
        Assertions.assertThat(prataPage.toList().get(0).getName()).isEqualTo(expectedName);
    }

    // Teste para verificar se a lista completa é retornada corretamente
    @Test
    @DisplayName("listAll returns list of prata when successful")
    void listAll_ReturnsListOfPratas_WhenSuccessful() {
        String expectedName = PrataCreator.createValidPrata().getName();

        // Invoca o método de listagem sem paginação
        List<Prata> pratas = prataController.listAll().getBody();

        // Validações
        Assertions.assertThat(pratas)
                .isNotNull()
                .isNotEmpty()
                .hasSize(1);
        Assertions.assertThat(pratas.get(0).getName()).isEqualTo(expectedName);
    }

    // Teste para buscar por ID
    @Test
    @DisplayName("findById returns prata when successful")
    void findById_ReturnsPrata_WhenSuccessful() {
        Long expectedId = PrataCreator.createValidPrata().getId();

        // Invoca o método de busca por ID
        Prata prata = prataController.findById(1).getBody();

        // Validações
        Assertions.assertThat(prata).isNotNull();
        Assertions.assertThat(prata.getId()).isNotNull().isEqualTo(expectedId);
    }

    // Teste para buscar por nome
    @Test
    @DisplayName("findByName returns a list of prata when successful")
    void findByName_ReturnsListOfPrata_WhenSuccessful() {
        String expectedName = PrataCreator.createValidPrata().getName();

        // Invoca o método de busca por nome
        List<Prata> pratas = prataController.findByName("prata").getBody();

        // Validações
        Assertions.assertThat(pratas)
                .isNotNull()
                .isNotEmpty()
                .hasSize(1);
        Assertions.assertThat(pratas.get(0).getName()).isEqualTo(expectedName);
    }

    // Teste para quando a busca por nome retorna uma lista vazia
    @Test
    @DisplayName("findByName returns an empty list of prata when prata is not found")
    void findByName_ReturnsEmptyListOfPrata_WhenPrataIsNotFound() {
        BDDMockito.when(prataServiceMock.findByName(ArgumentMatchers.anyString()))
                .thenReturn(Collections.emptyList()); // Configura o mock para retornar uma lista vazia

        // Invoca o método de busca por nome
        List<Prata> pratas = prataController.findByName("prata").getBody();

        // Validações
        Assertions.assertThat(pratas).isNotNull().isEmpty();
    }

    // Teste para salvar uma nova Prata
    @Test
    @DisplayName("save returns prata when successful")
    void save_ReturnsPrata_WhenSuccessful() {
        // Invoca o método de salvar
        Prata prata = prataController.save(PrataPostRequestBodyCreator.createPrataPostRequestBody()).getBody();

        // Validações
        Assertions.assertThat(prata).isNotNull().isEqualTo(PrataCreator.createValidPrata());
    }

    // Teste para atualizar uma Prata
    @Test
    @DisplayName("replace updates prata when successful")
    void replace_UpdatesPrata_WhenSuccessful() {
        // Valida que o método não lança exceções
        Assertions.assertThatCode(() -> prataController.replace(PrataPutRequestBodyCreator.createPrataPutRequestBody()))
                .doesNotThrowAnyException();

        // Valida o retorno da atualização
        ResponseEntity<Void> entity = prataController.replace(PrataPutRequestBodyCreator.createPrataPutRequestBody());
        Assertions.assertThat(entity).isNotNull();
        Assertions.assertThat(entity.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT);
    }

    // Teste para excluir uma Prata
    @Test
    @DisplayName("delete removes prata when successful")
    void delete_RemovesPrata_WhenSuccessful() {
        // Valida que o método não lança exceções
        Assertions.assertThatCode(() -> prataController.delete(1))
                .doesNotThrowAnyException();

        // Valida o retorno da exclusão
        ResponseEntity<Void> entity = prataController.delete(1);
        Assertions.assertThat(entity).isNotNull();
        Assertions.assertThat(entity.getStatusCode()).isEqualTo(HttpStatus.NO_CONTENT);
    }
}
```
