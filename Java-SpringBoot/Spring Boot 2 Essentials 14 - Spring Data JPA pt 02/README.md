## Spring Boot 2 Essentials 14 - Spring Data JPA pt 02

`repository` - extends da classe `JpaRepository`, ai passa a `classe` que representa esse repositório aqui é Prata e o atributo que representa o `id`;

`spring` - funciona da seguinte forma ele tem esses métodos que vão criar o `Selects` automaticamete baseado na expressão no nome do método que você escrever aqui, vamos utilizar o que já tem disponível no `JpaRepository`;

o `spring` automaticamente identificou esse repositorio PrataRepository como spring bean (componentes gerenciados pelo Spring);

```java
package br.com.pkpratas01.repository;

import br.com.pkpratas01.domain.Prata;

import org.springframework.data.jpa.repository.JpaRepository;

public interface PrataRepository extends JpaRepository<Prata, Long> {

}
```

Quando tem os dominios como entidades e for la no controller voce vai ver que tem warning falando que nao deve utilizar as classes que representam a tabela de banco de dados;

Cria um novo pacote que `DTO` ou `request` com a classe `PrataPostRequestBody.java` é uma classe DTO no `post` não precisa de `id`;

- Quando utiliza dessa forma esta aumentando ainda mais a coesão;

```java
package br.com.pkpratas01.request;

import lombok.Data;

@Data
public class PrataPostRequestBody {
    private String name;
}
```

```java
// Definindo o pacote do controller
package br.com.pkpratas01.controller;

// Importação de bibliotecas e pacotes necessários
import lombok.RequiredArgsConstructor;
import lombok.extern.log4j.Log4j2;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

// Importação de classes do domínio, serviços e utilitários
import br.com.pkpratas01.domain.Prata;
import br.com.pkpratas01.request.PrataPostRequestBody;
import br.com.pkpratas01.request.PrataPutRequestBody;
import br.com.pkpratas01.service.PrataService;
import br.com.pkpratas01.util.DateUtil;

import java.time.LocalDateTime;
import java.util.List;

// Anotação que define essa classe como um controller REST
@RestController
// Definindo o caminho base para as rotas dessa classe (http://localhost:8080/pratas)
@RequestMapping("pratas")
// Ativando o log com Log4j2
@Log4j2
// Lombok para gerar automaticamente o construtor com todos os parâmetros
@RequiredArgsConstructor
public class PrataController {

    // Injeção de dependência das classes necessárias para formatação de data e serviço de prata
    private final DateUtil dateUtil;
    private final PrataService prataService;

    // Método GET que retorna todas as pratas
    @GetMapping
    public ResponseEntity<List<Prata>> list() {
        // Logando a data e hora atual formatada para o banco de dados
        log.info(dateUtil.formatLocalDateTimeToDatabaseStyle(LocalDateTime.now()));
        // Retorna a lista de pratas no corpo da resposta com o status 200 OK
        return ResponseEntity.ok(prataService.listAll());
    }

    // Método GET que retorna uma prata pelo id
    @GetMapping(path = "/{id}")
    public ResponseEntity<Prata> findById(@PathVariable long id) {
        // Encontra uma prata por id, ou lança uma exceção se não encontrado
        return ResponseEntity.ok(prataService.findByIdOrThrowBadRequestException(id));
    }

    // Método POST que salva uma nova prata
    @PostMapping
    public ResponseEntity<Prata> save(@RequestBody PrataPostRequestBody prataPostRequestBody){
        // Salva a prata recebida no corpo da requisição e retorna com status 201 (Criado)
        return new ResponseEntity<>(prataService.save(prataPostRequestBody), HttpStatus.CREATED);
    }

    // Método DELETE que deleta uma prata pelo id
    @DeleteMapping(path = "/{id}")
    public ResponseEntity<Void> delete(@PathVariable long id) {
        // Deleta a prata com o id informado
        prataService.delete(id);
        // Retorna resposta com status 204 (Sem conteúdo) após a exclusão
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }

    // Método PUT que substitui os dados de uma prata
    @PutMapping
    public ResponseEntity<Void> replace(@RequestBody PrataPutRequestBody prataPutRequestBody) {
        // Substitui a prata com os dados recebidos
        prataService.replace(prataPutRequestBody);
        // Retorna resposta com status 204 (Sem conteúdo) após a substituição
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}

```

```java
// Definindo o pacote do serviço
package br.com.pkpratas01.service;

// Importação de bibliotecas necessárias
import java.util.List;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Service;
import org.springframework.web.server.ResponseStatusException;
import br.com.pkpratas01.domain.Prata;
import br.com.pkpratas01.repository.PrataRepository;
import br.com.pkpratas01.request.PrataPostRequestBody;
import br.com.pkpratas01.request.PrataPutRequestBody;
import lombok.RequiredArgsConstructor;

// Anotação que define essa classe como um serviço no Spring
@Service
// Lombok para gerar automaticamente o construtor com todos os parâmetros
@RequiredArgsConstructor
public class PrataService {

    // Injeção de dependência do repositório que interage com o banco de dados
    private final PrataRepository prataRepository;

    // Método para listar todas as pratas
    public List<Prata> listAll() {
        // Retorna todas as pratas do banco de dados usando o repositório
        return prataRepository.findAll();
    }

    // Método para buscar uma prata por id, ou lançar uma exceção se não encontrada
    public Prata findByIdOrThrowBadRequestException(long id) {
        // Tenta encontrar uma prata no banco. Se não encontrar, lança uma exceção com status 400
        return prataRepository.findById(id)
                .orElseThrow(() -> new ResponseStatusException(HttpStatus.BAD_REQUEST, "Prata not Found"));
    }

    // Método para salvar uma nova prata
    public Prata save(PrataPostRequestBody prataPostRequestBody) {
        // Cria e salva a nova prata no banco, utilizando os dados recebidos no corpo da requisição
        return prataRepository.save(Prata.builder().name(prataPostRequestBody.getName()).build());
    }

    // Método para deletar uma prata
    public void delete(long id) {
        // Deleta a prata encontrada pelo id, caso ela exista
        prataRepository.delete(findByIdOrThrowBadRequestException(id));
    }

    // Método para substituir uma prata existente
    public void replace(PrataPutRequestBody prataPutRequestBody) {
        // Encontra a prata existente pelo id no corpo da requisição
        Prata savedPrata = findByIdOrThrowBadRequestException(prataPutRequestBody.getId());
        // Cria uma nova instância de Prata com os dados atualizados
        Prata prata = Prata.builder()
                .id(savedPrata.getId()) // Mantém o id da prata existente
                .name(prataPutRequestBody.getName()) // Atualiza o nome da prata
                .build();
        // Salva a prata atualizada no banco de dados
        prataRepository.save(prata);
    }
}

```
