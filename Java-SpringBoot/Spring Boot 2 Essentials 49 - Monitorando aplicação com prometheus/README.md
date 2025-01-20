## Spring Boot 2 Essentials 49 - Monitorando aplicação com prometheus

**https://prometheus.io/**

Essa aplicação vai trabalhar fazendo pool de dados da sua aplicação spring boot, vai guardar tudo em um banco de dados utilizado para esse tipo de informação;

Existe outras aplicações aonde eles recebe como se fosse um push, spring boot mandaria para ele;

`prometheus.yml`

```yml
global:
  scrape_interval: 15s # By default, scrape targets every 15 seconds.

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
#  external_labels:
#    monitor: 'codelab-monitor'

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  #  - job_name: 'prometheus'
  #
  #    # Override the global default and scrape targets from this job every 5 seconds.
  #    scrape_interval: 5s
  #
  #    static_configs:
  #      - targets: ['localhost:9090']

  - job_name: "springboot-essentials-actuator"

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s
    metrics_path: "/actuator/prometheus"
    static_configs:
      - targets: ["host.docker.internal:8080"]
```

`application.yml`

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
        include: info, health, metrics, prometheus

info:
  app: Spring Boot 2 Essentials By DevDojo
  website: https://devdojo.academy
  github: https://github.com/devdojobr/sprinngboot2-essentials
```
`docker-compose.yml`
````yml
version: '3.1'
services:
  db:
    image: mysql
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
    - "3306:3306"
    volumes:
    - devdojo_data:/var/lib/mysql

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
    - "./src/main/resources/prometheus.yml:/etc/prometheus/prometheus.yml"
    command:
    - "--config.file=/etc/prometheus/prometheus.yml"
    ports:
    - "9090:9090"

volumes:
  devdojo_data:
````