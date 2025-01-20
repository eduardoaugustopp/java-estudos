## Spring Boot 2 Essentials 50 - Monitorando métricas com gráficos no Grafana

O objetivo é ter uma melhor vizualização das métricas, vamos utilizar `Grafana` - vai trabalhar em conjunto com `Prometheus` e o foco é fazer dashboards;

`docker-compose.yml`

```yaml
version: "3.1"
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

  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
volumes:
  devdojo_data:
```

**http://localhost:3000** - admin/admin -> skip

````java
package academy.devdojo.springboot2;

import io.micrometer.core.instrument.MeterRegistry;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.actuate.autoconfigure.metrics.MeterRegistryCustomizer;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Springboot2EssentialsApplication {

	public static void main(String[] args) {
		SpringApplication.run(Springboot2EssentialsApplication.class, args);
	}

	@Bean
	MeterRegistryCustomizer<MeterRegistry> configurer(
			@Value("${spring.application.name}") String applicationName) {
		return (registry) -> registry.config().commonTags("application", applicationName);
	}
}
````