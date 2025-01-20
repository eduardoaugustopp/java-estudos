## Spring Boot 2 Essentials 33 - Unit Tests pt 01

Criar testes unitários para o Controller;

`@InjectMocks` - utiliza quando você quer testar a classe em si;

`@Mock` - utiliza para todas as classes que estão sendo utilizadas dentro desse PrataController;

```java
// O pacote onde esta classe está localizada
package br.com.pkpratas01.util;

// Importa a classe Prata do pacote domain
import br.com.pkpratas01.domain.Prata;

// Declaração da classe utilitária PrataCreator
public class PrataCreator {

    // Método estático que cria e retorna um objeto Prata a ser salvo no banco de dados
    public static Prata createPrataToBeSaved() {
        // Cria um novo objeto Prata usando o padrão builder
        // Aqui, apenas define o nome como "Corrente"
        return Prata.builder()
                .name("Corrente") // Define o nome da prata
                .build(); // Constrói o objeto e o retorna
    }

    // Método estático que cria e retorna um objeto Prata já válido (ou seja, com ID)
    public static Prata createValidPrata() {
        // Cria um novo objeto Prata com um nome e um ID
        // Este ID representa que o objeto já foi salvo no banco (simulação de entidade válida)
        return Prata.builder()
                .name("Corrente") // Define o nome da prata
                .id(1L) // Define o ID como 1L (Long)
                .build(); // Constrói o objeto e o retorna
    }

    // Método estático que cria e retorna um objeto Prata atualizado
    public static Prata createValidUpdatedPrata() {
        // Este método cria um objeto Prata com um ID e o mesmo nome
        // Ele pode ser usado para simular uma atualização de dados
        return Prata.builder()
                .name("Corrente") // Define o nome atualizado da prata
                .id(1L) // Mantém o mesmo ID como 1L (Long)
                .build(); // Constrói o objeto e o retorna
    }
}
```

```java
package br.com.pkpratas01.controller;

// Importação de pacotes necessários
import java.util.List;

import org.assertj.core.api.Assertions; // Usado para fazer asserções nos testes
import org.junit.jupiter.api.BeforeEach; // Executa um método antes de cada teste
import org.junit.jupiter.api.DisplayName; // Adiciona um nome descritivo aos testes
import org.junit.jupiter.api.Test; // Marca um método como um teste
import org.junit.jupiter.api.extension.ExtendWith; // Integra o Spring com o JUnit 5
import org.mockito.ArgumentMatchers; // Permite criar correspondências genéricas para argumentos nos mocks
import org.mockito.BDDMockito; // Fornece suporte para criar stubs no estilo BDD (Behavior-Driven Development)
import org.mockito.InjectMocks; // Injeta mocks em objetos para teste
import org.mockito.Mock; // Cria um mock para simular o comportamento de dependências
import org.springframework.data.domain.Page; // Representa uma página de resultados (usada em paginação)
import org.springframework.data.domain.PageImpl; // Implementação concreta de Page
import org.springframework.test.context.junit.jupiter.SpringExtension; // Suporte para testes no Spring com JUnit 5

import br.com.pkpratas01.domain.Prata; // Representa o domínio "Prata"
import br.com.pkpratas01.service.PrataService; // Serviço que contém a lógica de negócios para Prata
import br.com.pkpratas01.util.PrataCreator; // Classe utilitária para criar objetos de teste de Prata

// Anotação que habilita a integração entre Spring e JUnit 5
@ExtendWith(SpringExtension.class)
public class PrataControllerTest {
    // Injeta automaticamente o mock de PrataService no PrataController
    @InjectMocks
    private PrataController prataController;

    // Cria um mock para simular o comportamento de PrataService
    @Mock
    private PrataService prataServiceMock;

    // Método que será executado antes de cada teste
    @BeforeEach
    void setUp() {
        // Cria uma página de objetos Prata usando a implementação PageImpl
        PageImpl<Prata> prataPage = new PageImpl<>(List.of(PrataCreator.createValidPrata()));

        // Configura o mock para retornar a página de Prata quando o método listAll for chamado
        BDDMockito.when(prataServiceMock.listAll(ArgumentMatchers.any()))
                .thenReturn(prataPage);
    }

    // Método de teste para verificar o comportamento do método list no controller
    @Test
    @DisplayName("List returns list of prata inside page object when successful") // Descrição do teste
    void list_ReturnsListOfPratasInsidePageObject_WhenSuccessful() {
        // Obtém o nome esperado do objeto Prata criado pelo PrataCreator
        String expectedName = PrataCreator.createValidPrata().getName();

        // Chama o método list no controller e captura o corpo da resposta
        Page<Prata> prataPage = prataController.list(null).getBody();

        // Verifica se o resultado não é nulo
        Assertions.assertThat(prataPage).isNotNull();

        // Verifica se a lista dentro da página não está vazia e contém um elemento
        Assertions.assertThat(prataPage.toList())
                .isNotEmpty()
                .hasSize(1);

        // Verifica se o nome do primeiro elemento da lista é igual ao nome esperado
        Assertions.assertThat(prataPage.toList().get(0).getName()).isEqualTo(expectedName);
    }
}
```
