## Spring Boot 2 Essentials 42 - Spring Security pt 03 - Authentication Principal e página padrão de

**https://www.blitter.se/utils/basic-authentication-header-generator/**

**https://www.base64decode.org/**

```java
package academy.devdojo.springboot2.config;

import lombok.extern.log4j.Log4j2;  // Importa o Log4j2 para facilitar a criação de logs
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder; // Classe responsável pela configuração de autenticação
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity; // Habilita a segurança para métodos
import org.springframework.security.config.annotation.web.builders.HttpSecurity; // Classe responsável pelas configurações de segurança na web
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity; // Ativa a segurança na aplicação web
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter; // Classe que permite configurar a segurança da aplicação
import org.springframework.security.crypto.factory.PasswordEncoderFactories; // Utiliza a fábrica para criar um codificador de senhas
import org.springframework.security.crypto.password.PasswordEncoder; // Interface para codificar as senhas

@EnableWebSecurity  // Anotação que habilita a segurança web na aplicação
@Log4j2  // Anotação para habilitar o log com Log4j2
@EnableGlobalMethodSecurity(prePostEnabled = true)  // Habilita a segurança global para os métodos com anotações de segurança (ex: @PreAuthorize)
public class SecurityConfig extends WebSecurityConfigurerAdapter {  // Extende a classe WebSecurityConfigurerAdapter para personalizar a segurança

    /**
     * Aqui você configura as regras de segurança para a aplicação
     * BasicAuthenticationFilter - Filtro para autenticação básica
     * UsernamePasswordAuthenticationFilter - Filtro de autenticação com usuário e senha
     * DefaultLoginPageGeneratingFilter - Gera a página de login padrão
     * DefaultLogoutPageGeneratingFilter - Gera a página de logout padrão
     * FilterSecurityInterceptor - Intercepta e decide se o usuário tem permissão para acessar determinado recurso
     * Authentication -> Authorization - A autenticação é feita primeiro (verificação da identidade) e depois a autorização (verificação das permissões)
     *
     * @param http Objeto HttpSecurity para configurar as regras de segurança
     * @throws Exception Exceção que pode ser lançada durante a configuração
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()  // Desabilita a proteção contra CSRF (Cross-Site Request Forgery) - não recomendado em produção sem outras medidas de segurança
//                csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())  // Essa linha está comentada, mas seria uma forma de configurar o CSRF com cookies
                .authorizeRequests()  // Autoriza a configuração das permissões de acesso para as requisições
                .anyRequest()  // Qualquer requisição
                .authenticated()  // Exige que o usuário esteja autenticado
                .and()  // Para encadear outras configurações
                .formLogin()  // Habilita o login via formulário padrão
                .and()  // Encadeando
                .httpBasic();  // Habilita a autenticação básica HTTP (usuário e senha no cabeçalho)
    }

    /**
     * Método responsável pela configuração de autenticação
     * Aqui você define como os usuários serão autenticados na sua aplicação
     *
     * @param auth Objeto AuthenticationManagerBuilder para configurar os detalhes da autenticação
     * @throws Exception Exceção que pode ser lançada durante a configuração
     */
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        PasswordEncoder passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();  // Cria um codificador de senha
        log.info("Password encoded {}", passwordEncoder.encode("test"));  // Loga a senha codificada para testes (não deve ser usado em produção)

        auth.inMemoryAuthentication()  // Configura a autenticação na memória (para testes, não recomendado para produção)
                .withUser("william")  // Cria um usuário com o nome 'william'
                .password(passwordEncoder.encode("academy"))  // Codifica a senha do usuário
                .roles("USER", "ADMIN")  // Define os papéis/roles do usuário 'william'
                .and()  // Encadeando
                .withUser("devdojo")  // Cria um outro usuário chamado 'devdojo'
                .password(passwordEncoder.encode("academy"))  // Codifica a senha para o usuário 'devdojo'
                .roles("USER");  // Define apenas o papel 'USER' para o 'devdojo'
    }
}
```

```java
// Pacote onde o controlador está localizado.
package academy.devdojo.springboot2.controller;

// Importações necessárias para o funcionamento do código
import academy.devdojo.springboot2.domain.Anime;
import academy.devdojo.springboot2.requests.AnimePostRequestBody;
import academy.devdojo.springboot2.requests.AnimePutRequestBody;
import academy.devdojo.springboot2.service.AnimeService;
import lombok.RequiredArgsConstructor;
import lombok.extern.log4j.Log4j2;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.util.List;

// Define que esta classe é um controlador REST, ou seja, lida com requisições HTTP.
@RestController
@RequestMapping("animes")  // O caminho da URL para acessar os recursos será /animes
@Log4j2  // Ativa o Log4j2 para registro de logs.
@RequiredArgsConstructor  // Gera o construtor necessário para a injeção de dependências.
public class AnimeController {

    // Injeção do serviço AnimeService para utilizar seus métodos.
    private final AnimeService animeService;

    // Método que retorna uma lista de animes com paginação.
    @GetMapping
    public ResponseEntity<Page<Anime>> list(Pageable pageable) {
        // Chama o serviço para listar os animes com paginação.
        return ResponseEntity.ok(animeService.listAll(pageable));
    }

    // Método que retorna uma lista de todos os animes sem paginação.
    @GetMapping(path = "/all")
    public ResponseEntity<List<Anime>> listAll() {
        // Chama o serviço para listar todos os animes.
        return ResponseEntity.ok(animeService.listAllNonPageable());
    }

    // Método que busca um anime pelo seu ID.
    @GetMapping(path = "/{id}")
    public ResponseEntity<Anime> findById(@PathVariable long id) {
        // Chama o serviço para buscar o anime pelo ID.
        return ResponseEntity.ok(animeService.findByIdOrThrowBadRequestException(id));
    }

    // Método que também busca um anime pelo ID, mas usa informações de autenticação do usuário.
    @GetMapping(path = "by-id/{id}")
    public ResponseEntity<Anime> findByIdAuthenticationPrincipal(@PathVariable long id,
                                                                 @AuthenticationPrincipal UserDetails userDetails) {
        // Loga as informações do usuário autenticado.
        log.info(userDetails);
        // Chama o serviço para buscar o anime pelo ID.
        return ResponseEntity.ok(animeService.findByIdOrThrowBadRequestException(id));
    }

    // Método que busca animes por nome.
    @GetMapping(path = "/find")
    public ResponseEntity<List<Anime>> findByName(@RequestParam String name) {
        // Chama o serviço para buscar animes pelo nome.
        return ResponseEntity.ok(animeService.findByName(name));
    }

    // Método que cria um novo anime. Apenas usuários com o papel de ADMIN podem fazer isso.
    @PostMapping
    @PreAuthorize("hasRole('ADMIN')")  // Verifica se o usuário tem o papel de ADMIN antes de permitir o acesso.
    public ResponseEntity<Anime> save(@RequestBody @Valid AnimePostRequestBody animePostRequestBody) {
        // Chama o serviço para salvar o novo anime e retorna uma resposta com o anime criado.
        return new ResponseEntity<>(animeService.save(animePostRequestBody), HttpStatus.CREATED);
    }

    // Método que deleta um anime com base no seu ID.
    @DeleteMapping(path = "/{id}")
    public ResponseEntity<Void> delete(@PathVariable long id) {
        // Chama o serviço para excluir o anime com o ID fornecido.
        animeService.delete(id);
        // Retorna uma resposta sem conteúdo após a exclusão.
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }

    // Método que substitui um anime existente por um novo anime.
    @PutMapping
    public ResponseEntity<Void> replace(@RequestBody AnimePutRequestBody animePutRequestBody) {
        // Chama o serviço para substituir o anime com os dados fornecidos.
        animeService.replace(animePutRequestBody);
        // Retorna uma resposta sem conteúdo após a substituição.
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}
```
