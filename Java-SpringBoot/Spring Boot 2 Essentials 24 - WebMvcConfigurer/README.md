## Spring Boot 2 Essentials 24 - WebMvcConfigurer

Como trocar o padrão do tamanho das páginas que retornamos;

Esta com 5 páginas:

```json
{
  "content": [
    {
      "id": 2,
      "name": "TestEEEEE"
    },
    {
      "id": 3,
      "name": "Teste 7"
    },
    {
      "id": 4,
      "name": "Teste 5555555555555555"
    },
    {
      "id": 5,
      "name": "Teste 444444444"
    },
    {
      "id": 6,
      "name": "Teste 444444444"
    }
  ],
  "pageable": {
    "pageNumber": 0,
    "pageSize": 5,
    "sort": {
      "sorted": false,
      "unsorted": true,
      "empty": true
    },
    "offset": 0,
    "paged": true,
    "unpaged": false
  },
  "totalPages": 5,
  "totalElements": 21,
  "last": false,
  "first": true,
  "size": 5,
  "number": 0,
  "sort": {
    "sorted": false,
    "unsorted": true,
    "empty": true
  },
  "numberOfElements": 5,
  "empty": false
}
```

Precisa fazer uma adição no código porque vai afetar o spring como um todo ou seja precisa criar uma nova configuração;

Cria pacote `configurer` e uma classe `PkPratas01WebMvcConfigurer`;

```java
package br.com.pkpratas01.configurer;  // Pacote onde a classe está localizada, nesse caso, "configurer"

import java.util.List;  // Importando a classe List, que é utilizada para manipulação de listas de objetos

import org.springframework.context.annotation.Configuration;  // Importando a anotação @Configuration que indica que a classe é uma classe de configuração do Spring
import org.springframework.data.domain.PageRequest;  // Importando a classe PageRequest para criar uma solicitação de página
import org.springframework.data.web.PageableHandlerMethodArgumentResolver;  // Importando o resolutor de argumentos para paginação
import org.springframework.web.method.support.HandlerMethodArgumentResolver;  // Importando a interface que permite resolver os argumentos dos métodos de controle
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;  // Importando a interface WebMvcConfigurer para personalizar configurações do Spring MVC

@Configuration  // A anotação @Configuration indica que a classe contém configurações para o Spring.
public class PkPratas01WebMvcConfigurer implements WebMvcConfigurer {  // A classe implementa WebMvcConfigurer, que permite configurar várias opções do Spring MVC.

    // O método addArgumentResolvers permite adicionar resolvers personalizados para os argumentos dos métodos de controle
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {

        // Criando um objeto de PageableHandlerMethodArgumentResolver, que lida com o argumento de paginação em métodos de controle
        PageableHandlerMethodArgumentResolver pageHandler = new PageableHandlerMethodArgumentResolver();

        // Definindo uma página padrão (0, 10) caso o parâmetro de paginação não seja fornecido na requisição
        pageHandler.setFallbackPageable(PageRequest.of(0, 10));

        // Adicionando o resolver à lista de resolvers, permitindo que o Spring use a paginação automaticamente nos métodos de controle
        resolvers.add(pageHandler);
    }
}
```

**http://localhost:8080/pratas?page=0**
