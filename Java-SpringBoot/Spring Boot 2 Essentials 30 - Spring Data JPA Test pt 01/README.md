## Spring Boot 2 Essentials 30 - Spring Data JPA Test pt 01

Um banco que todas as vezes que executar os testes, o banco vai ser criado e destruido logo em seguida:

```xml
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

`spring-boot-starter-test` já inclui o JUnit;

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

```java
package br.com.pkpratas01.repository;

import org.assertj.core.api.Assertions; // Importa a biblioteca de asserções (assertions) para verificar se os testes estão corretos.
import org.junit.jupiter.api.DisplayName; // Importa a anotação para definir um nome legível para o teste.
import org.junit.jupiter.api.Test; // Importa a anotação para marcar o método como um teste.
import org.springframework.beans.factory.annotation.Autowired; // Importa a anotação para injetar dependências automaticamente.
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest; // Importa a anotação para configurar o teste com JPA e banco de dados.

import br.com.pkpratas01.domain.Prata; // Importa a classe Prata do pacote domain.

@DataJpaTest // Anotação que configura o Spring para testar apenas a camada JPA, ou seja, a persistência de dados no banco.
@DisplayName("Tests for Prata Repository") // Define o nome do teste para torná-lo mais legível.
class PrataRepositoryTest { // Define a classe de teste para o repositório Prata.

    @Autowired // O Spring injeta automaticamente uma instância do repositório PrataRepository para ser usada no teste.
    private PrataRepository prataRepository;

    @Test // Anotação que indica que o método é um teste.
    @DisplayName("Save creates prata when Successful") // Define o nome do teste.
    void save_PersistPrata_WhenSuccessful() { // Método de teste que verifica se a persistência da prata no banco ocorre com sucesso.

        // Cria um objeto de prata para ser salvo.
        Prata prataToBeSaved = createPrata();

        // Salva o objeto de prata no banco de dados, usando o repositório.
        Prata prataSaved = this.prataRepository.save(prataToBeSaved);

        // Verifica se o objeto salvo não é nulo, ou seja, se o save foi bem-sucedido.
        Assertions.assertThat(prataSaved).isNotNull();

        // Verifica se o ID do objeto salvo foi gerado corretamente (não nulo).
        Assertions.assertThat(prataSaved.getId()).isNotNull();

        // Verifica se o nome da prata salva é igual ao nome da prata que foi criada antes.
        Assertions.assertThat(prataSaved.getName()).isEqualTo(prataToBeSaved.getName());
    }

    // Método auxiliar para criar um objeto de prata com dados definidos.
    private Prata createPrata() {
        return Prata.builder() // Usa o builder da classe Prata para criar o objeto.
                .name("Anel") // Define o nome da prata como "Anel".
                .build(); // Conclui a construção do objeto.
    }
}
```
