## Spring Boot 2 Essentials 12 - Instalando Docker e executando container MySQL

`docker-comnpose.yml`

```yml
version: "3.8"
services:
  db:
    image: mysql
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: root
    ports:
      - "3307:3306"
    volumes:
      - pk_data:/var/lib/mysql

volumes:
  pk_data:
    driver: local
```

No terminal `docker-comnpose up`;

- Container que esta rodando:

```bash
PS C:\Users\eduar\OneDrive\Área de Trabalho\Nova pasta\pkpratas01-backend> docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                               NAMES
f7e6f905b029   mysql     "docker-entrypoint.s…"   4 minutes ago   Up 4 minutes   33060/tcp, 0.0.0.0:3307->3306/tcp   mysql
PS C:\Users\eduar\OneDrive\Área de Trabalho\Nova pasta\pkpratas01-backend>
```

- `docker ps -a` - lista todos os containers que tem;
