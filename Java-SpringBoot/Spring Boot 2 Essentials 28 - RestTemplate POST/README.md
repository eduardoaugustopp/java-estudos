## Spring Boot 2 Essentials 28 - RestTemplate POST

```java
// Cria um objeto "Prata" com o nome "corrente" usando o padrão Builder.
Prata corrente = Prata.builder().name("corrente").build();

// Envia uma requisição POST para a URL "http://localhost:8080/pratas/".
// O RestTemplate é usado para realizar a comunicação HTTP. Neste caso, é uma requisição que envia os dados do objeto "corrente" para o servidor.
// A resposta será armazenada na variável "correnteSaved", que será um objeto ResponseEntity que contém a resposta do servidor.
ResponseEntity<Prata> correnteSaved = new RestTemplate().exchange(
        "http://localhost:8080/pratas/",            // URL do servidor para o qual a requisição é enviada
        HttpMethod.POST,                            // Método HTTP (POST é usado para criar recursos)
        new HttpEntity<>(corrente, createJsonHeader()), // O corpo da requisição com o objeto corrente e o cabeçalho JSON
        Prata.class                                  // O tipo da resposta esperada (Prata.class significa que esperamos um objeto do tipo Prata)
);

// Loga no console a resposta recebida do servidor (o objeto "correnteSaved" que foi retornado após a criação do "Prata").
log.info("saved prata {}", correnteSaved);

// Função auxiliar que cria um cabeçalho HTTP com o tipo de conteúdo "application/json".
// Isso informa ao servidor que estamos enviando e recebendo dados no formato JSON.
private static HttpHeaders createJsonHeader() {
    HttpHeaders httpHeaders = new HttpHeaders();
    httpHeaders.setContentType(MediaType.APPLICATION_JSON);  // Define o cabeçalho "Content-Type" como "application/json"
    return httpHeaders;  // Retorna os cabeçalhos configurados
}

```

**NÃO CONSEGUI VER NO CONSOLE**
