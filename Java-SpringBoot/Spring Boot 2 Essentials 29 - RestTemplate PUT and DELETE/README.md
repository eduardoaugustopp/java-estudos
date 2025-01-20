## Spring Boot 2 Essentials 29 - RestTemplate PUT and DELETE

`PUT e DELETE` - retornam void então você não sabe sabe o que aconteceu com aquela requisição e bom ter o status http como retorno porque esta executando uma requisição para um servidor que provavelmente é remoto e eu quero saber o que aconteceu com aquele servidor;

```java
// Prata 'prataToBeUpdated' é obtida a partir de 'correnteSaved.getBody()'
// que provavelmente representa o objeto corrente que foi retornado anteriormente de uma requisição HTTP.
Prata prataToBeUpdated = correnteSaved.getBody();

// Aqui estamos alterando o nome da prata. O 'setName' muda o valor do nome da prata.
prataToBeUpdated.setName("Corrente 2");

// Criando uma nova requisição HTTP para atualizar o objeto 'prataToBeUpdated' no servidor.
// A URL para a requisição PUT é fornecida, passando o objeto prataToBeUpdated no corpo da requisição.
// Também é criada um cabeçalho com a função 'createJsonHeader()' para indicar que o corpo é no formato JSON.
ResponseEntity<Void> correnteUpdated = new RestTemplate().exchange(
    "http://localhost:8080/pratas/", // URL do servidor para fazer a requisição PUT
    HttpMethod.PUT, // Tipo da requisição (neste caso, PUT para atualizar)
    new HttpEntity<>(prataToBeUpdated, createJsonHeader()), // Corpo da requisição (o objeto prataToBeUpdated) e os cabeçalhos
    Void.class // O tipo de resposta esperado (neste caso, sem conteúdo, por isso Void)
);

// Exibindo as informações da resposta da requisição PUT no log.
log.info(correnteUpdated);

// Agora estamos fazendo uma requisição DELETE para excluir a prata criada anteriormente.
// A URL recebe o ID da prata para identificar qual item deve ser deletado.
ResponseEntity<Void> correnteDeleted = new RestTemplate().exchange(
    "http://localhost:8080/pratas/{id}", // URL do servidor para fazer a requisição DELETE, com o ID da prata
    HttpMethod.DELETE, // Tipo da requisição (neste caso, DELETE para excluir)
    null, // Não há corpo na requisição DELETE, então é 'null'
    Void.class, // O tipo de resposta esperado (neste caso, sem conteúdo, por isso Void)
    prataToBeUpdated.getId() // O ID da prata para ser deletada
);

// Exibindo as informações da resposta da requisição DELETE no log.
log.info(correnteDeleted);
```

**NÃO CONSEGUI APLICAR NO CONSOLE**
