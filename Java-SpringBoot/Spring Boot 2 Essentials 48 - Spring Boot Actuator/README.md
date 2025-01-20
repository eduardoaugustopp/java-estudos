## Spring Boot 2 Essentials 48 - Spring Boot Actuator

O **Spring Boot Actuator** é uma biblioteca que fornece funcionalidades de monitoramento e gerenciamento de aplicativos Spring Boot em produção. Ele expõe vários pontos de extremidade úteis, conhecidos como endpoints, para visualizar o estado do aplicativo, obter métricas e controlar diversos aspectos do sistema.

**Endpoints prontos para monitoramento:**

**/actuator/health:** Fornece informações sobre a saúde do aplicativo, incluindo conectividade com bancos de dados, serviços externos, etc.

**/actuator/metrics:** Exibe métricas do sistema, como uso de memória, tempo de resposta, e outras métricas detalhadas.

**/actuator/info:** Exibe informações gerais do aplicativo, como versão, descrição e outras configurações.

**/actuator/env:** Mostra as propriedades de ambiente do Spring, como variáveis de ambiente, configurações de sistema e propriedades do Spring.

**Monitoramento e métricas:**

- O Actuator coleta métricas de uso, como a quantidade de requisições feitas, tempo de resposta, e a utilização de recursos como CPU e memória.

**Segurança:**

- É possível adicionar segurança aos endpoints para garantir que informações sensíveis não sejam acessadas sem autenticação.

**Configuração:**

- O Actuator pode ser facilmente configurado através de propriedades no arquivo application.properties ou application.yml.

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

@EnableWebSecurity
@Log4j2
@EnableGlobalMethodSecurity(prePostEnabled = true)
@RequiredArgsConstructor
@SuppressWarnings("java:S5344")
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    private final DevDojoUserDetailsService devDojoUserDetailsService;

    /**
     * BasicAuthenticationFilter
     * UsernamePasswordAuthenticationFilter
     * DefaultLoginPageGeneratingFilter
     * DefaultLogoutPageGeneratingFilter
     * FilterSecurityInterceptor
     * Authentication -> Authorization
     *
     * @param http
     * @throws Exception
     */
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
//                csrf().csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
                .authorizeRequests()
                .antMatchers("/animes/admin/**").hasRole("ADMIN")
                .antMatchers("/animes/**").hasRole("USER")
                .antMatchers("/actuator/**").permitAll()
                .anyRequest()
                .authenticated()
                .and()
                .formLogin()
                .and()
                .httpBasic();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        PasswordEncoder passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
        log.info("Password encoded {}", passwordEncoder.encode("academy"));

        auth.inMemoryAuthentication()
                .withUser("william2")
                .password(passwordEncoder.encode("academy"))
                .roles("USER", "ADMIN")
                .and()
                .withUser("devdojo2")
                .password(passwordEncoder.encode("academy"))
                .roles("USER");

        auth.userDetailsService(devDojoUserDetailsService)
                .passwordEncoder(passwordEncoder);
    }
}
```

```yml
server:
  error:
    include-stacktrace: on_param

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/anime?createDatabaseIfNotExist=true
    username: root
    password: root
  jpa:
    hibernate:
      ddl-auto: update

logging:
  level:
    org:
      hibernate:
        SQL: DEBUG

management:
  endpoints:
    web:
      exposure:
        include: info, health, metrics

info:
  app: Spring Boot 2 Essentials By DevDojo
  website: https://devdojo.academy
  github: https://github.com/devdojobr/sprinngboot2-essentials
```
