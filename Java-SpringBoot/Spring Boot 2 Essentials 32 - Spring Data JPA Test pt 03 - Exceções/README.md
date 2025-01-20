## Spring Boot 2 Essentials 32 - Spring Data JPA Test pt 03 - Exceções

```java
// Anotação Lombok que gera automaticamente os métodos "getter", "setter",
// "toString", "equals" e "hashCode" para todos os campos da classe.
@Data

// Anotação Lombok que cria um construtor com todos os argumentos necessários
// (ou seja, inclui todos os atributos da classe como parâmetros).
@AllArgsConstructor

// Anotação Lombok que cria um construtor sem argumentos.
// Esse construtor vazio é necessário para algumas bibliotecas, como JPA.
@NoArgsConstructor

// Indica que esta classe é uma entidade JPA, ou seja, representa uma tabela no banco de dados.
@Entity

// Anotação Lombok que fornece um padrão de projeto "Builder".
// Permite criar objetos de maneira mais legível e fluida.
@Builder
public class Prata {

    // Indica que este campo é a chave primária da entidade.
    @Id
    // Especifica que o valor do ID será gerado automaticamente pelo banco de dados.
    // O strategy `GenerationType.IDENTITY` significa que o banco gerará o ID
    // (por exemplo, usando uma sequência ou auto-incremento).
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    // Indica que o campo `name` não pode ser vazio ou nulo.
    // Caso a validação seja violada, será lançada uma exceção (ConstraintViolationException)
    // com a mensagem personalizada fornecida.
    @NotEmpty(message = "The prata name cannot be empty")
    private String name;
}
```

```java
// Importação de dependências necessárias para o teste
// @Test é uma anotação usada para indicar que este método é um teste.
@Test
// @DisplayName serve para fornecer um nome descritivo ao teste, útil em relatórios.
@DisplayName("Save throw ConstrainValidationException when is empty")
void save_ThrowsConstrainValidationException_WhenNameIsEmpty() {
    // Criamos uma nova instância da classe Prata. Esta classe provavelmente
    // representa uma entidade do banco de dados.
    Prata prata = new Prata();

    // Não configuramos nenhum valor para os atributos de "prata". Por isso, o nome
    // (ou outro campo obrigatório) está vazio, o que deve violar as regras de validação.

    // Usamos o AssertJ para verificar se uma exceção do tipo ConstraintViolationException
    // será lançada quando tentarmos salvar o objeto "prata".
    // A ideia é garantir que o sistema não permita salvar um objeto inválido.
    Assertions.assertThatExceptionOfType(ConstraintViolationException.class)
        // Esta linha define que a exceção deve ser lançada ao tentar salvar "prata".
        .isThrownBy(() -> this.prataRepository.save(prata))
        // Esta linha verifica se a mensagem da exceção contém um texto específico,
        // garantindo que a validação foi feita corretamente.
        .withMessageContaining("The prata name cannot be empty");
}
```
