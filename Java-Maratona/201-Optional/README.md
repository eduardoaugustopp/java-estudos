## Optional pt 01

`optional` - uma classe, o retorno desse método pode não vir; de vez retornar nulo, retorna um optional;
- é um objeto que vai encapsular outros objeto;

**`EXEMPLO CHATGPT`**

**Cenário**

Queremos buscar o nome de um usuário por ID, mas o usuário pode não existir no sistema. Sem o uso de Optional, corremos o risco de obter um NullPointerException.

````java
import java.util.Optional;

public class OptionalExample {

    public static void main(String[] args) {
        // Simula uma busca de usuário pelo ID
        Optional<String> userName = findUserById(1);

        // Verifica se o valor está presente e imprime
        userName.ifPresentOrElse(
            name -> System.out.println("Usuário encontrado: " + name),
            () -> System.out.println("Usuário não encontrado")
        );

        // Outra maneira: Usando um valor padrão se não for encontrado
        String nameOrDefault = userName.orElse("Usuário padrão");
        System.out.println("Resultado: " + nameOrDefault);
    }

    // Método que retorna um Optional simulando a busca de um usuário
    private static Optional<String> findUserById(int id) {
        // Simulação: Retorna um usuário para ID 1, mas vazio para outros IDs
        if (id == 1) {
            return Optional.of("Eduardo");
        } else {
            return Optional.empty(); // Representa ausência de valor
        }
    }
}
````