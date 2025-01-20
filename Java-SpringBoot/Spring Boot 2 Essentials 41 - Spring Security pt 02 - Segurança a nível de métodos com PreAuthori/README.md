## Spring Boot 2 Essentials 41 - Spring Security pt 02 - Segurança a nível de métodos com PreAuthori

```java
package academy.devdojo.springboot2.config;

import lombok.extern.log4j.Log4j2;  // Usado para gerar logs
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;  // Classe para configurar o gerenciamento de autenticação
import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;  // Habilita segurança para métodos
import org.springframework.security.config.annotation.web.builders.HttpSecurity;  // Classe para configurar as regras de segurança de requisições HTTP
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;  // Habilita a configuração de segurança da web
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;  // Classe base para configurar a segurança da web
import org.springframework.security.crypto.factory.PasswordEncoderFactories;  // Usado para criar um codificador de senhas
import org.springframework.security.crypto.password.PasswordEncoder;  // Interface para codificação de senhas

@EnableWebSecurity  // Anotação que habilita a configuração de segurança da web
@Log4j2  // Anotação que ativa o log usando o Log4j2
@EnableGlobalMethodSecurity(prePostEnabled = true)  // Habilita a segurança baseada em anotações nos métodos, permitindo usar @PreAuthorize, @Secured, etc.
public class SecurityConfig extends WebSecurityConfigurerAdapter {  // Classe que estende WebSecurityConfigurerAdapter para personalizar a segurança

    // Configuração das regras de segurança para requisições HTTP
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()  // Desabilita a proteção CSRF (Cross-Site Request Forgery), geralmente usado para APIs
            // .csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse()) // Comentado, mas aqui seria um exemplo de como configurar o repositório de tokens CSRF com cookies
            .authorizeRequests()  // Inicia a configuração de regras de autorização para requisições HTTP
            .anyRequest()  // Define que qualquer requisição precisa ser autenticada
            .authenticated()  // Requer autenticação para qualquer requisição
            .and()  // Finaliza a configuração anterior
            .httpBasic();  // Define que a autenticação será feita via HTTP Basic (usuário e senha enviados no cabeçalho da requisição)
    }

    // Configuração de autenticação (definindo usuários e senhas)
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        PasswordEncoder passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();  // Cria um codificador de senhas para codificar as senhas de usuários
        log.info("Password encoded {}", passwordEncoder.encode("test"));  // Exibe um log com a senha codificada para "test"

        // Configura usuários em memória com senhas codificadas
        auth.inMemoryAuthentication()  // Utiliza a autenticação em memória (sem banco de dados)
            .withUser("william")  // Define o usuário "william"
            .password(passwordEncoder.encode("academy"))  // Codifica a senha do usuário
            .roles("USER", "ADMIN")  // Define as permissões (roles) do usuário
            .and()  // Finaliza a configuração do usuário anterior
            .withUser("devdojo")  // Define outro usuário, "devdojo"
            .password(passwordEncoder.encode("academy"))  // Codifica a senha deste usuário
            .roles("USER");  // Define a role (permissão) deste usuário
    }
}
```

```java
// Pacote e importações do projeto
package academy.devdojo.springboot2.controller;

import academy.devdojo.springboot2.domain.Anime; // Importa a classe Anime, que é uma entidade
import academy.devdojo.springboot2.requests.AnimePostRequestBody; // Classe de requisição para criar um Anime
import academy.devdojo.springboot2.requests.AnimePutRequestBody; // Classe de requisição para atualizar um Anime
import academy.devdojo.springboot2.service.AnimeService; // Classe de serviço que contém a lógica de negócio
import lombok.RequiredArgsConstructor; // Usado para gerar o construtor automaticamente
import lombok.extern.log4j.Log4j2; // Usado para gerar logs
import org.springframework.data.domain.Page; // Utilizado para lidar com páginas de resultados
import org.springframework.data.domain.Pageable; // Usado para fazer paginação
import org.springframework.http.HttpStatus; // Definindo o status HTTP da resposta
import org.springframework.http.ResponseEntity; // Classe para criar respostas HTTP
import org.springframework.security.access.prepost.PreAuthorize; // Permissões de acesso com base no papel (role)
import org.springframework.web.bind.annotation.*; // Anotações do Spring para controlar as rotas HTTP

import javax.validation.Valid; // Para validação de dados de entrada
import java.util.List; // Lista de resultados
```
