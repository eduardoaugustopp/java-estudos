## Spring Boot 2 Essentials 44 - Spring Security pt 05 - Proteção de URL com Antmatcher

```java
package academy.devdojo.springboot2.config;

// Importação das classes necessárias para configuração de segurança no Spring Boot.
import academy.devdojo.springboot2.service.DevDojoUserDetailsService;
import lombok.RequiredArgsConstructor;
import lombok.extern.log4j.Log4j2;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;
import org.springframework.security.crypto.password.PasswordEncoder;

// Habilita a segurança da aplicação
@EnableWebSecurity

// Habilita a possibilidade de usar anotações como @PreAuthorize para controlar acesso por métodos
@EnableGlobalMethodSecurity(prePostEnabled = true)

// Adiciona logs com a biblioteca Log4j2
@Log4j2

// Cria um construtor automaticamente com os argumentos finais obrigatórios
@RequiredArgsConstructor

// Marca a classe como uma configuração de segurança personalizada
@SuppressWarnings("java:S5344") // Supressão de um aviso específico de segurança do SonarLint
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    // Serviço personalizado que implementa a lógica de carregamento de usuários
    private final DevDojoUserDetailsService devDojoUserDetailsService;

    /**
     * Configuração da segurança HTTP. Define como as requisições HTTP serão protegidas.
     *
     * @param http Configuração de segurança HTTP
     * @throws Exception Lança exceção em caso de erro
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable() // Desativa a proteção CSRF (não recomendado para produção)
            // Se fosse habilitar, poderia usar CookieCsrfTokenRepository:
            // .csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
            .authorizeRequests() // Começa a definição das regras de autorização
            .antMatchers("/animes/admin/**").hasRole("ADMIN") // Apenas usuários com o papel ADMIN podem acessar URLs que começam com "/animes/admin/"
            .antMatchers("/animes/**").hasRole("USER") // Apenas usuários com o papel USER podem acessar URLs que começam com "/animes/"
            .anyRequest().authenticated() // Qualquer outra requisição precisa estar autenticada
            .and()
            .formLogin() // Habilita o formulário de login padrão do Spring Security
            .and()
            .httpBasic(); // Habilita autenticação HTTP Basic
    }

    /**
     * Configuração da autenticação, como o armazenamento de usuários e senhas.
     *
     * @param auth Gerenciador de autenticação
     * @throws Exception Lança exceção em caso de erro
     */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // Cria um codificador de senhas que suporta múltiplos tipos de codificação
        PasswordEncoder passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();

        // Exemplo de como o passwordEncoder codifica uma senha e a exibe no log
        log.info("Password encoded {}", passwordEncoder.encode("academy"));

        // Adiciona usuários em memória com suas respectivas credenciais e papéis
        auth.inMemoryAuthentication()
            .withUser("william2") // Cria um usuário chamado "william2"
            .password(passwordEncoder.encode("academy")) // Define a senha codificada
            .roles("USER", "ADMIN") // Define os papéis (roles) deste usuário
            .and()
            .withUser("devdojo2") // Cria um segundo usuário chamado "devdojo2"
            .password(passwordEncoder.encode("academy")) // Define a senha codificada
            .roles("USER"); // Define apenas o papel USER para este usuário

        // Configura um serviço customizado para carregar usuários do banco de dados
        auth.userDetailsService(devDojoUserDetailsService)
            .passwordEncoder(passwordEncoder); // Define o codificador de senha para os usuários do banco
    }
}
```

```java
// Importações necessárias para as funcionalidades, como validações, controle HTTP, segurança e logs.
package academy.devdojo.springboot2.controller;

import academy.devdojo.springboot2.domain.Anime; // Entidade Anime
import academy.devdojo.springboot2.requests.AnimePostRequestBody; // DTO para POST
import academy.devdojo.springboot2.requests.AnimePutRequestBody; // DTO para PUT
import academy.devdojo.springboot2.service.AnimeService; // Serviço que contém a lógica de negócio
import lombok.RequiredArgsConstructor; // Cria o construtor com todos os campos finais
import lombok.extern.log4j.Log4j2; // Para logs com Log4j2
import org.springframework.data.domain.Page; // Para paginação
import org.springframework.data.domain.Pageable; // Representa parâmetros de paginação
import org.springframework.http.HttpStatus; // Define os status HTTP
import org.springframework.http.ResponseEntity; // Representa uma resposta HTTP
import org.springframework.security.access.prepost.PreAuthorize; // Controle de acesso baseado em roles
import org.springframework.security.core.annotation.AuthenticationPrincipal; // Obtem detalhes do usuário autenticado
import org.springframework.security.core.userdetails.UserDetails; // Detalhes do usuário autenticado
import org.springframework.web.bind.annotation.*; // Anotações para REST API

import javax.validation.Valid; // Validações de dados recebidos no corpo da requisição
import java.util.List; // Manipulação de listas

// Declaração de um controlador REST
@RestController
@RequestMapping("animes") // Define o caminho base para todos os endpoints do controlador
@Log4j2 // Habilita logs usando Log4j2
@RequiredArgsConstructor // Cria um construtor automaticamente para os campos finais (final)
public class AnimeController {
    private final AnimeService animeService; // Dependência do serviço de anime, inicializada via construtor

    // Endpoint GET para listar animes com paginação
    @GetMapping
    public ResponseEntity<Page<Anime>> list(Pageable pageable) {
        // Retorna a lista de animes paginada no corpo da resposta
        return ResponseEntity.ok(animeService.listAll(pageable));
    }

    // Endpoint GET para listar todos os animes sem paginação
    @GetMapping(path = "/all")
    public ResponseEntity<List<Anime>> listAll() {
        // Chama o serviço para obter todos os animes e os retorna na resposta
        return ResponseEntity.ok(animeService.listAllNonPageable());
    }

    // Endpoint GET para buscar um anime pelo ID
    @GetMapping(path = "/{id}")
    public ResponseEntity<Anime> findById(@PathVariable long id) {
        // Busca o anime pelo ID, ou lança uma exceção caso não seja encontrado
        return ResponseEntity.ok(animeService.findByIdOrThrowBadRequestException(id));
    }

    // Endpoint GET para buscar um anime pelo ID com detalhes do usuário autenticado
    @GetMapping(path = "by-id/{id}")
    public ResponseEntity<Anime> findByIdAuthenticationPrincipal(@PathVariable long id,
                                                                 @AuthenticationPrincipal UserDetails userDetails) {
        // Loga os detalhes do usuário autenticado
        log.info(userDetails);
        // Busca o anime pelo ID
        return ResponseEntity.ok(animeService.findByIdOrThrowBadRequestException(id));
    }

    // Endpoint GET para buscar animes pelo nome
    @GetMapping(path = "/find")
    public ResponseEntity<List<Anime>> findByName(@RequestParam String name) {
        // Retorna a lista de animes cujo nome contém a string fornecida
        return ResponseEntity.ok(animeService.findByName(name));
    }

    // Endpoint POST para salvar um novo anime
    @PostMapping
    public ResponseEntity<Anime> save(@RequestBody @Valid AnimePostRequestBody animePostRequestBody) {
        // Salva o anime e retorna o objeto salvo com status HTTP 201 (Created)
        return new ResponseEntity<>(animeService.save(animePostRequestBody), HttpStatus.CREATED);
    }

    // Endpoint DELETE para excluir um anime pelo ID (acessível apenas para administradores)
    @DeleteMapping(path = "/admin/{id}")
    public ResponseEntity<Void> delete(@PathVariable long id) {
        // Chama o serviço para deletar o anime
        animeService.delete(id);
        // Retorna status HTTP 204 (No Content) indicando sucesso sem corpo na resposta
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }

    // Endpoint PUT para substituir/atualizar um anime
    @PutMapping
    public ResponseEntity<Void> replace(@RequestBody AnimePutRequestBody animePutRequestBody) {
        // Chama o serviço para atualizar o anime com base no corpo da requisição
        animeService.replace(animePutRequestBody);
        // Retorna status HTTP 204 (No Content) indicando sucesso
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}
```
