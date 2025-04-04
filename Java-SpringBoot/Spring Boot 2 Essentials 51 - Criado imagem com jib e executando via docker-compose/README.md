## Spring Boot 2 Essentials 51 - Criado imagem com jib e executando via docker-compose

```yml
version: "2.4"
x-database-variables: &database-variables
  SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/anime?createDatabaseIfNotExist=true
  SPRING_DATASOURCE_USERNAME: root
  SPRING_DATASOURCE_PASSWORD: root

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
    mem_limit: 512m

  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - "./src/main/resources/prometheus.yml:/etc/prometheus/prometheus.yml"
    command: "--config.file=/etc/prometheus/prometheus.yml"
    ports:
      - "9090:9090"
    mem_limit: 128m

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    mem_limit: 128m

  springboot2-essentials:
    image: williamsuane/springboot2-essentials:0.0.1-SNAPSHOT
    ports:
      - "8080:8080"
    environment:
      <<: *database-variables
    mem_limit: 512m

volumes:
  devdojo_data:
```
