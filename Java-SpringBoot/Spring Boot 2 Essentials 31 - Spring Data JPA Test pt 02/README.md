## Spring Boot 2 Essentials 31 - Spring Data JPA Test pt 02

```java
package br.com.pkpratas01.repository;

// Importando as classes necessárias para os testes
import java.util.List;
import java.util.Optional;

import org.assertj.core.api.Assertions; // Framework para asserções em testes
import org.junit.jupiter.api.DisplayName; // Anotação para dar um nome aos testes
import org.junit.jupiter.api.Test; // Anotação para marcar métodos de teste
import org.springframework.beans.factory.annotation.Autowired; // Injeção de dependência
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest; // Anotação para testes de repositórios JPA

import br.com.pkpratas01.domain.Prata; // Classe de domínio Prata
import lombok.extern.log4j.Log4j2; // Logger para log de informações

@DataJpaTest // Indica que o teste é para um repositório JPA, ou seja, interage com o banco de dados
@DisplayName("Tests for Prata Repository") // Nome do conjunto de testes
@Log4j2 // Usado para log de mensagens
class PrataRepositoryTest {

    // Injeção automática do repositório para testar operações de banco de dados
    @Autowired
    private PrataRepository prataRepository;

    // Teste para garantir que ao salvar um 'Prata', ele seja persistido no banco
    @Test
    @DisplayName("Save persists prata when Successful") // Nome do teste
    void save_PersistPrata_WhenSuccessful() {
        // Criando um objeto 'Prata' a ser salvo
        Prata prataToBeSaved = createPrata();

        // Salvando o 'Prata' no banco de dados
        Prata prataSaved = this.prataRepository.save(prataToBeSaved);

        // Verificando se o objeto foi salvo corretamente (não é nulo)
        Assertions.assertThat(prataSaved).isNotNull();

        // Verificando se o ID do 'Prata' gerado é não nulo (significa que foi persistido)
        Assertions.assertThat(prataSaved.getId()).isNotNull();

        // Verificando se o nome do 'Prata' salvo é igual ao nome do 'Prata' criado
        Assertions.assertThat(prataSaved.getName()).isEqualTo(prataToBeSaved.getName());
    }

    // Teste para garantir que ao salvar um 'Prata' com um novo nome, o nome seja atualizado
    @Test
    @DisplayName("Save updates prata when Successful") // Nome do teste
    void save_UpdatesPrata_WhenSuccessful() {
        // Criando um objeto 'Prata' a ser salvo
        Prata prataToBeSaved = createPrata();

        // Salvando o 'Prata' no banco
        Prata prataSaved = this.prataRepository.save(prataToBeSaved);

        // Alterando o nome do 'Prata'
        prataSaved.setName("Colar");

        // Salvando novamente para atualizar os dados
        Prata prataUpdated = this.prataRepository.save(prataSaved);

        // Verificando se o objeto foi salvo e atualizado corretamente
        Assertions.assertThat(prataUpdated).isNotNull();
        Assertions.assertThat(prataUpdated.getId()).isNotNull();
        Assertions.assertThat(prataUpdated.getName()).isEqualTo(prataSaved.getName()); // Nome atualizado
    }

    // Teste para garantir que ao deletar um 'Prata', ele seja removido do banco
    @Test
    @DisplayName("Delete removes prata when Successful") // Nome do teste
    void delete_RemovesPrata_WhenSuccessful() {
        // Criando um 'Prata' a ser salvo
        Prata prataToBeSaved = createPrata();

        // Salvando o 'Prata' no banco
        Prata prataSaved = this.prataRepository.save(prataToBeSaved);

        // Deletando o 'Prata'
        this.prataRepository.delete(prataSaved);

        // Verificando se o 'Prata' foi removido (o retorno deve ser vazio)
        Optional<Prata> prataOptional = this.prataRepository.findById(prataSaved.getId());

        // Verificando se o 'Prata' não existe mais
        Assertions.assertThat(prataOptional).isEmpty();
    }

    // Teste para garantir que ao buscar um 'Prata' pelo nome, uma lista de 'Prata' seja retornada
    @Test
    @DisplayName("Find By Name returns list of prata when Successful") // Nome do teste
    void findByName_ReturnsListOfPrata_WhenSuccessful() {
        // Criando e salvando um 'Prata'
        Prata prataToBeSaved = createPrata();
        Prata prataSaved = this.prataRepository.save(prataToBeSaved);

        // Pegando o nome do 'Prata' salvo
        String name = prataSaved.getName();

        // Buscando o 'Prata' pelo nome
        List<Prata> pratas = this.prataRepository.findByName(name);

        // Verificando se a lista retornada não está vazia
        Assertions.assertThat(pratas).isNotEmpty();

        // Verificando se a lista contém o 'Prata' que foi salvo
        Assertions.assertThat(pratas).contains(prataSaved);
    }

    // Teste para garantir que ao buscar um 'Prata' por nome que não existe, a lista retornada será vazia
    @Test
    @DisplayName("Find By Name returns empty list when no prata is found") // Nome do teste
    void findByName_ReturnsEmptyList_WhenPrataIsNotFound() {
        // Buscando por um 'Prata' que não existe
        List<Prata> pratas = this.prataRepository.findByName("Relógio");

        // Verificando que a lista está vazia
        Assertions.assertThat(pratas).isEmpty();
    }

    // Método auxiliar para criar um objeto 'Prata' com um nome específico
    private Prata createPrata() {
        return Prata.builder()
                .name("Escapulário") // Nome do 'Prata'
                .build();
    }
}

```
