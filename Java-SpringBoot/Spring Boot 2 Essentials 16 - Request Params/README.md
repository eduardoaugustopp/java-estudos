## Spring Boot 2 Essentials 16 - Request Params

`Request Params` - adicionar parametros na URL, para que possamos fazer a busca de prata por nome;

```java
import java.util.List;
// Importa a classe List, que é usada para armazenar coleções de objetos.

import org.springframework.data.jpa.repository.JpaRepository;
// Importa a interface JpaRepository, que fornece métodos prontos para manipulação de dados no banco de dados.

public interface PrataRepository extends JpaRepository<Prata, Long> {
    // Declaração de uma interface chamada PrataRepository que estende JpaRepository.
    // JpaRepository já oferece métodos padrão como save, delete, findAll, etc.

    List<Prata> findByName(String name);
    // Método personalizado para buscar todas as entidades "Prata" que possuem o campo "name" igual ao valor fornecido.
    // O Spring Data JPA interpreta automaticamente o "findByName" e cria uma consulta SQL baseada nisso.
}
```

Pacote service duplica:

```java
public List<Prata> listAll() {
    // Método que retorna uma lista contendo todos os registros da entidade "Prata" no banco de dados.
    return prataRepository.findAll();
    // Chama o método findAll() do repositório (prataRepository), que é um método padrão do JpaRepository.
    // Esse método retorna uma lista com todos os objetos do tipo "Prata" que estão no banco de dados.
}

public List<Prata> findByName(String name) {
    // Método que retorna uma lista contendo todos os registros da entidade "Prata" cujo campo "name"
    // é igual ao valor fornecido como parâmetro.
    return prataRepository.findByName(name);
    // Chama o método findByName(String name) do repositório (prataRepository).
    // Esse método foi definido no PrataRepository e utiliza o Spring Data JPA para buscar no banco de dados
    // todos os registros cujo nome corresponde ao parâmetro "name".
}
```

Pacote controller duplica GetMapping:

**http://localhost:8080/pratas/find?name=TestEEEEE**

```java
  @GetMapping(path = "/find")
    public ResponseEntity<List<Prata>> findByName(@RequestParam String name) {
        return ResponseEntity.ok(prataService.findByName(name));
    }
```
