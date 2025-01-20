## Spring Boot 2 Essentials 40 - Spring Security pt 01 - CSRF Token

```java
package academy.devdojo.springboot2.config; // Pacote onde o arquivo está localizado

import lombok.extern.log4j.Log4j2; // Importando a anotação Log4j2 para utilizar logs
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder; // Importando a classe para configurar autenticação
import org.springframework.security.config.annotation.web.builders.HttpSecurity; // Importando a classe para configurar segurança HTTP
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity; // Anotação para habilitar segurança na aplicação
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter; // Classe base para configurar segurança web
import org.springframework.security.crypto.factory.PasswordEncoderFactories; // Classe para criar codificadores de senha
import org.springframework.security.crypto.password.PasswordEncoder; // Interface para codificadores de senha

@EnableWebSecurity // Habilita a segurança no Spring Security
@Log4j2 // Habilita o log utilizando Log4j2
public class SecurityConfig extends WebSecurityConfigurerAdapter { // Classe de configuração de segurança estendendo a classe base

    // Método para configurar a segurança HTTP
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // Desativa a proteção CSRF (Cross-Site Request Forgery)
        http.csrf().disable()
            // Configura as permissões de requisições HTTP
            .authorizeRequests()
            .anyRequest() // Permite qualquer requisição
            .authenticated() // Requer autenticação para qualquer requisição
            .and()
            .httpBasic(); // Ativa a autenticação básica (onde o navegador solicita usuário e senha)
    }

    // Método para configurar a autenticação de usuários
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // Cria um codificador de senha (utiliza um codificador padrão do Spring)
        PasswordEncoder passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();

        // Faz o log da senha codificada
        log.info("Password encoded {}", passwordEncoder.encode("test"));

        // Configura a autenticação na memória (em vez de usar banco de dados ou outro provedor)
        auth.inMemoryAuthentication()
            // Define um usuário com o nome "william", senha "academy" e dois papéis: USER e ADMIN
            .withUser("william")
            .password(passwordEncoder.encode("academy")) // Codifica a senha
            .roles("USER", "ADMIN") // Define as permissões (roles) para o usuário
            .and()
            // Define outro usuário com o nome "devdojo", senha "academy" e papel apenas USER
            .withUser("devdojo")
            .password(passwordEncoder.encode("academy"))
            .roles("USER");
    }
}

```
