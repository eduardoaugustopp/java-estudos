## Spring Boot 2 Essentials 43 - Spring Security pt 04 - Autenticação com usuário no banco de dados

```java
package academy.devdojo.springboot2.config;

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

@EnableWebSecurity // Habilita a configuração de segurança da aplicação
@Log4j2 // Permite o uso de logging no código (log)
@EnableGlobalMethodSecurity(prePostEnabled = true) // Habilita a segurança em métodos (usando @PreAuthorize, @Secured, etc.)
@RequiredArgsConstructor // Gera o construtor para a classe com os campos finais
@SuppressWarnings("java:S5344") // Ignora o aviso de segurança relacionado ao uso do Lombok
public class SecurityConfig extends WebSecurityConfigurerAdapter { // Classe de configuração de segurança

    private final DevDojoUserDetailsService devDojoUserDetailsService; // Serviço para carregar os detalhes do usuário

    /**
     * Este método configura como as requisições HTTP serão tratadas pela segurança
     *
     * @param http Objeto HttpSecurity para configurar as regras de segurança
     * @throws Exception Exceção em caso de falha na configuração
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable() // Desabilita a proteção contra CSRF (Cross Site Request Forgery) - é uma boa prática desabilitar em APIs REST
//                csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse()) // Comentado, mas seria uma alternativa para configurar CSRF com cookies
                .authorizeRequests() // Inicia a configuração de autorização das requisições
                .anyRequest() // Para qualquer requisição
                .authenticated() // Requer autenticação
                .and()
                .formLogin() // Habilita o login via formulário
                .and()
                .httpBasic(); // Habilita a autenticação básica (usuário e senha em cabeçalho HTTP)
    }

    /**
     * Este método configura a autenticação de usuários
     *
     * @param auth Objeto AuthenticationManagerBuilder para configurar a autenticação
     * @throws Exception Exceção em caso de falha na configuração
     */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // Cria um codificador de senha que delega a codificação para o algoritmo correto
        PasswordEncoder passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
        log.info("Password encoded {}", passwordEncoder.encode("academy")); // Log para ver a senha codificada

        // Configura a autenticação em memória com dois usuários
        auth.inMemoryAuthentication()
                .withUser("william2") // Usuário "william2"
                .password(passwordEncoder.encode("academy")) // Senha codificada
                .roles("USER", "ADMIN") // Papéis atribuídos ao usuário
                .and()
                .withUser("devdojo2") // Outro usuário "devdojo2"
                .password(passwordEncoder.encode("academy")) // Senha codificada
                .roles("USER"); // Somente o papel "USER"

        // Configura o serviço de detalhes do usuário (DevDojoUserDetailsService) para autenticação personalizada
        auth.userDetailsService(devDojoUserDetailsService)
                .passwordEncoder(passwordEncoder); // Codificador de senha usado para verificar a senha dos usuários
    }
}
```

```java
// Importações das classes e pacotes necessários
package academy.devdojo.springboot2.controller;

import academy.devdojo.springboot2.domain.Anime; // Importa o modelo Anime
import academy.devdojo.springboot2.requests.AnimePostRequestBody; // Importa o corpo da requisição para criar um Anime
import academy.devdojo.springboot2.requests.AnimePutRequestBody; // Importa o corpo da requisição para atualizar um Anime
import academy.devdojo.springboot2.service.AnimeService; // Importa o serviço que manipula as lógicas de negócio para Anime
import lombok.RequiredArgsConstructor; // Lombok para gerar o construtor automaticamente
import lombok.extern.log4j.Log4j2; // Lombok para gerar logs de forma fácil
import org.springframework.data.domain.Page; // Classe do Spring Data para suporte a paginação
import org.springframework.data.domain.Pageable; // Classe do Spring Data para controle de página
import org.springframework.http.HttpStatus; // Classe do Spring para definir status HTTP
import org.springframework.http.ResponseEntity; // Classe do Spring que envolve o corpo da resposta e o status HTTP
import org.springframework.security.access.prepost.PreAuthorize; // Permite controle de acesso com roles no Spring Security
import org.springframework.security.core.annotation.AuthenticationPrincipal; // Recupera o usuário autenticado
import org.springframework.security.core.userdetails.UserDetails; // Interface que representa os detalhes do usuário autenticado
import org.springframework.web.bind.annotation.*; // Anotações para mapear as requisições HTTP

import javax.validation.Valid; // Para validação das requisições com anotações como @NotNull, @Size, etc.
import java.util.List; // Lista para armazenar múltiplos elementos

// Define a classe como um controlador REST
@RestController
// Define o caminho base para todas as requisições dentro desse controlador
@RequestMapping("animes")
// Anotação do Lombok para gerar os logs
@Log4j2
// Anotação do Lombok para gerar o construtor com os parâmetros necessários
@RequiredArgsConstructor
public class AnimeController {
    // Injeção de dependência do serviço AnimeService
    private final AnimeService animeService;

    // Método para listar todos os animes de forma paginada
    @GetMapping
    public ResponseEntity<Page<Anime>> list(Pageable pageable) {
        // Retorna a lista paginada de animes utilizando o serviço
        return ResponseEntity.ok(animeService.listAll(pageable));
    }

    // Método para listar todos os animes sem paginação
    @GetMapping(path = "/all")
    public ResponseEntity<List<Anime>> listAll() {
        // Retorna a lista de animes sem paginação
        return ResponseEntity.ok(animeService.listAllNonPageable());
    }

    // Método para buscar um anime pelo ID
    @GetMapping(path = "/{id}")
    public ResponseEntity<Anime> findById(@PathVariable long id) {
        // Retorna o anime encontrado pelo ID ou lança uma exceção se não encontrar
        return ResponseEntity.ok(animeService.findByIdOrThrowBadRequestException(id));
    }

    // Método para buscar um anime pelo ID, mas exige que o usuário tenha a role "ADMIN"
    @GetMapping(path = "by-id/{id}")
    @PreAuthorize("hasRole('ADMIN')") // Verifica se o usuário tem a permissão de ADMIN
    public ResponseEntity<Anime> findByIdAuthenticationPrincipal(@PathVariable long id,
                                                                 @AuthenticationPrincipal UserDetails userDetails) {
        // Faz log do usuário que fez a requisição
        log.info(userDetails);
        // Retorna o anime encontrado pelo ID
        return ResponseEntity.ok(animeService.findByIdOrThrowBadRequestException(id));
    }

    // Método para buscar animes pelo nome
    @GetMapping(path = "/find")
    public ResponseEntity<List<Anime>> findByName(@RequestParam String name) {
        // Retorna a lista de animes filtrada pelo nome
        return ResponseEntity.ok(animeService.findByName(name));
    }

    // Método para criar um novo anime
    @PostMapping
    @PreAuthorize("hasRole('ADMIN')") // Verifica se o usuário tem a permissão de ADMIN
    public ResponseEntity<Anime> save(@RequestBody @Valid AnimePostRequestBody animePostRequestBody) {
        // Salva o novo anime e retorna a resposta com status CREATED (201)
        return new ResponseEntity<>(animeService.save(animePostRequestBody), HttpStatus.CREATED);
    }

    // Método para deletar um anime pelo ID
    @DeleteMapping(path = "/{id}")
    public ResponseEntity<Void> delete(@PathVariable long id) {
        // Deleta o anime pelo ID
        animeService.delete(id);
        // Retorna um status HTTP 204 No Content, indicando sucesso sem corpo
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }

    // Método para atualizar um anime
    @PutMapping
    public ResponseEntity<Void> replace(@RequestBody AnimePutRequestBody animePutRequestBody) {
        // Atualiza o anime com os dados do corpo da requisição
        animeService.replace(animePutRequestBody);
        // Retorna um status HTTP 204 No Content
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}
```

```java
package academy.devdojo.springboot2.domain;

// Importações de bibliotecas necessárias para o funcionamento do código.
import lombok.AllArgsConstructor; // Gera o construtor com todos os parâmetros.
import lombok.Builder; // Permite construir objetos de forma mais simples.
import lombok.Data; // Gera os métodos getters, setters, toString, equals e hashCode.
import lombok.NoArgsConstructor; // Gera o construtor sem parâmetros.
import org.springframework.security.core.GrantedAuthority; // Representa uma autoridade de segurança.
import org.springframework.security.core.authority.SimpleGrantedAuthority; // Classe simples que implementa GrantedAuthority.
import org.springframework.security.core.userdetails.UserDetails; // Interface do Spring Security para representar o usuário.

import javax.persistence.Entity; // Indica que a classe é uma entidade JPA, ou seja, será mapeada para uma tabela no banco de dados.
import javax.persistence.GeneratedValue; // Usado para indicar que o valor do campo será gerado automaticamente.
import javax.persistence.GenerationType; // Define o tipo de geração do valor para o campo.
import javax.persistence.Id; // Marca o campo como chave primária da entidade.
import javax.validation.constraints.NotEmpty; // Valida que o campo não pode ser vazio.
import java.util.Arrays; // Classe que facilita manipulação de arrays.
import java.util.Collection; // Interface para coleções de objetos.
import java.util.stream.Collectors; // Facilita o uso de streams para coletar elementos.

@Data // Anotação do Lombok que gera getters, setters, equals, hashCode e toString.
@AllArgsConstructor // Gera um construtor com todos os parâmetros.
@NoArgsConstructor // Gera um construtor sem parâmetros.
@Entity // Indica que esta classe é uma entidade JPA, mapeada para uma tabela no banco.
@Builder // Permite a criação de objetos de forma mais fácil e legível.
public class DevDojoUser implements UserDetails { // A classe implementa UserDetails, o que permite que o Spring Security entenda este objeto como um usuário.
    @Id // Marca a propriedade como a chave primária da tabela.
    @GeneratedValue(strategy = GenerationType.IDENTITY) // Indica que o valor do id será gerado automaticamente pelo banco de dados.
    private Long id; // A chave primária que identifica o usuário.

    @NotEmpty(message = "The anime name cannot be empty") // Valida que o nome não pode ser vazio.
    private String name; // Nome do usuário.

    private String username; // Nome de usuário para autenticação.

    private String password; // Senha do usuário.

    private String authorities; // Contém as permissões ou roles do usuário, separadas por vírgula.

    // Este método retorna as autoridades do usuário, que são as permissões ou papéis que ele possui.
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        // Aqui, o método divide a string 'authorities' (que contém os papéis) e cria uma lista de SimpleGrantedAuthority.
        return Arrays.stream(authorities.split(","))
                .map(SimpleGrantedAuthority::new) // Para cada papel, cria um SimpleGrantedAuthority.
                .collect(Collectors.toList()); // Coleta tudo em uma lista.
    }

    @Override
    public String getPassword() {
        return this.password; // Retorna a senha do usuário.
    }

    @Override
    public String getUsername() {
        return this.username; // Retorna o nome de usuário.
    }

    @Override
    public boolean isAccountNonExpired() {
        return true; // Sempre retorna true, indicando que a conta nunca expira.
    }

    @Override
    public boolean isAccountNonLocked() {
        return true; // Sempre retorna true, indicando que a conta nunca é bloqueada.
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true; // Sempre retorna true, indicando que as credenciais não expiram.
    }

    @Override
    public boolean isEnabled() {
        return true; // Sempre retorna true, indicando que a conta está habilitada.
    }
}
```

```java
// Importação da classe 'DevDojoUser', que é a entidade que representa o usuário no banco de dados.
// Essa classe deve estar mapeada com a tabela correspondente no banco.
import academy.devdojo.springboot2.domain.DevDojoUser;

// Importação da interface JpaRepository, que fornece métodos prontos para realizar operações no banco de dados.
import org.springframework.data.jpa.repository.JpaRepository;

// Definição da interface DevDojoUserRepository, que estende a interface JpaRepository.
// A interface JpaRepository já fornece vários métodos prontos para realizar operações de CRUD (Create, Read, Update, Delete) no banco de dados.
public interface DevDojoUserRepository extends JpaRepository<DevDojoUser, Long> {

    // Definindo um método que busca um 'DevDojoUser' no banco de dados com base no nome de usuário (username).
    // Este método é automaticamente implementado pelo Spring Data JPA com base no nome do método.
    DevDojoUser findByUsername(String username);
}
```

```java
// A anotação @Service indica que esta classe é um serviço que será gerenciado pelo Spring.
// O Spring irá instanciar essa classe automaticamente quando necessário.
@Service
// A anotação @RequiredArgsConstructor do Lombok cria automaticamente o construtor para os campos finais.
@RequiredArgsConstructor
public class DevDojoUserDetailsService implements UserDetailsService {
    // O DevDojoUserRepository é responsável por acessar os dados do banco relacionados ao usuário.
    // Ele é injetado aqui pelo Spring através do construtor gerado pelo Lombok.
    private final DevDojoUserRepository devDojoUserRepository;

    // O método loadUserByUsername é um método que deve ser implementado para carregar os detalhes do usuário
    // com base no nome de usuário que é passado como argumento. Esse método é utilizado pelo Spring Security
    // para autenticar o usuário.
    @Override
    public UserDetails loadUserByUsername(String username) {
        // O Optional.ofNullable verifica se o usuário foi encontrado. Se sim, retorna o usuário,
        // caso contrário, lança uma exceção.
        return Optional.ofNullable(devDojoUserRepository.findByUsername(username))
                // Caso o usuário não seja encontrado, lança uma exceção personalizada, dizendo que o usuário não foi encontrado.
                .orElseThrow(() -> new UsernameNotFoundException("DevDojo User not found"));
    }
}
```
