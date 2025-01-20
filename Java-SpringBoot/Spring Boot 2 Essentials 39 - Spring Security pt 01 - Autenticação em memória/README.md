## Spring Boot 2 Essentials 39 - Spring Security pt 01 - Autenticação em memória

Cria pacote `config`;

```java
// A anotação @EnableWebSecurity ativa as configurações de segurança do Spring Security
@EnableWebSecurity
// A anotação @Log4j2 ativa o log utilizando a biblioteca Log4j2
@Log4j2
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    // Esse método configura as permissões de acesso às URLs da aplicação
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        // Definindo que todas as requisições precisam ser autenticadas
        http.authorizeRequests()
                .anyRequest() // Qualquer requisição
                .authenticated() // Requer autenticação
                .and()
                .httpBasic(); // Usando autenticação básica (usuário e senha)
    }

    // Esse método configura os usuários e senhas em memória para autenticação
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        // Criando um PasswordEncoder que será usado para codificar as senhas
        PasswordEncoder passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();

        // Registrando a senha codificada no log para você visualizar
        log.info("Password encoded {}", passwordEncoder.encode("test"));

        // Configurando usuários em memória
        auth.inMemoryAuthentication()
                // Usuário "william" com a senha "academy" e os papéis (roles) "USER" e "ADMIN"
                .withUser("william")
                .password(passwordEncoder.encode("academy")) // Codificando a senha
                .roles("USER", "ADMIN")
                .and()
                // Usuário "devdojo" com a senha "academy" e o papel "USER"
                .withUser("devdojo")
                .password(passwordEncoder.encode("academy")) // Codificando a senha
                .roles("USER");
    }
}
```
