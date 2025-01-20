## Spring Boot 2 Essentials 04 - @Component, @Autowired, @SpringBootApplication

```java
package academy.devdojo.springboot2.util;

import java.time.LocalDateTime; // Importa a classe para representar data e hora sem fuso horário.
import java.time.format.DateTimeFormatter; // Importa a classe para formatar/parsear datas e horas.
import org.springframework.stereotype.Component; // Importa a anotação para declarar a classe como um componente Spring.

@Component // Marca esta classe como um componente gerenciado pelo Spring (bean). Pode ser injetada em outros lugares com @Autowired.
public class DateUtil {

    /**
     * Este método formata um objeto LocalDateTime para o formato padrão utilizado em bancos de dados.
     * @param localDateTime O objeto LocalDateTime que será formatado.
     * @return Uma String representando a data e hora no formato "yyyy-MM-dd HH:mm:ss".
     */
    public String formatLocalDateTimeToDatabaseStyle(LocalDateTime localDateTime) {
        // Cria um formatador com o padrão de data e hora usado por bancos de dados.
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

        // Aplica o formatador ao LocalDateTime e retorna o resultado como uma String.
        return formatter.format(localDateTime);
    }
}

```

```java
package academy.devdojo.springboot2.controller;

import academy.devdojo.springboot2.domain.Anime; // Importa a classe de domínio Anime.
import academy.devdojo.springboot2.util.DateUtil; // Importa a classe utilitária para formatar datas.
import java.time.LocalDateTime; // Importa a classe para representar data e hora.
import java.util.Arrays; // Importa a classe para criar listas de forma simples.
import java.util.List; // Importa a interface para listas.
import lombok.RequiredArgsConstructor; // Importa a anotação para gerar construtores para campos final.
import lombok.extern.slf4j.Slf4j; // Importa a anotação para adicionar suporte ao logger do SLF4J.
import org.springframework.web.bind.annotation.GetMapping; // Importa a anotação para mapear requisições GET.
import org.springframework.web.bind.annotation.RequestMapping; // Importa a anotação para definir a rota base.
import org.springframework.web.bind.annotation.RestController; // Importa a anotação para definir um controlador REST.

@RestController // Indica que esta classe é um controlador REST do Spring.
@RequestMapping("anime") // Define a rota base "/anime" para todas as requisições neste controlador.
@Slf4j // Adiciona suporte ao logger SLF4J para registrar logs.
@RequiredArgsConstructor // Gera automaticamente um construtor para os campos final, injetando dependências.
public class AnimeController {

    private final DateUtil dateUtil; // Dependência de DateUtil injetada automaticamente pelo Spring.

    /**
     * Endpoint para retornar uma lista de animes.
     * @return Uma lista de objetos Anime.
     */
    @GetMapping(path = "/list") // Mapeia a rota "/anime/list" para este método com o verbo HTTP GET.
    public List<Anime> listAll() {
        // Registra no log a data e hora atual formatada no estilo de banco de dados.
        log.info("Date Formatted {}", dateUtil.formatLocalDateTimeToDatabaseStyle(LocalDateTime.now()));

        // Retorna uma lista de animes como exemplo.
        return Arrays.asList(
            new Anime("Boku No Hero"), // Adiciona "Boku No Hero" à lista.
            new Anime("Berserk"), // Adiciona "Berserk" à lista.
            new Anime("Naruto") // Adiciona "Naruto" à lista.
        );
    }
}

```

```java
package academy.devdojo.springboot2;

import org.springframework.boot.SpringApplication; // Classe usada para inicializar a aplicação Spring Boot.
import org.springframework.boot.autoconfigure.SpringBootApplication; // Anotação que configura automaticamente o ambiente Spring Boot.

@SpringBootApplication // Marca esta classe como a principal para inicializar o Spring Boot.
public class Springboot2EssentialsApplication {

    /**
     * O método principal (main) é o ponto de entrada da aplicação.
     * @param args Argumentos da linha de comando (opcionalmente usados na inicialização da aplicação).
     */
    public static void main(String[] args) {
        // Inicializa a aplicação Spring Boot. Este método configura o contexto da aplicação
        // e realiza a varredura (scan) para encontrar componentes, controladores e outros beans do Spring.
        SpringApplication.run(Springboot2EssentialsApplication.class, args);
    }
}
```

## Artigo sobre injeção

**https://reflectoring.io/constructor-injection/**
