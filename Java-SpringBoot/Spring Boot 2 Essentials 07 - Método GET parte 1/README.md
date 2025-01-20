## Spring Boot 2 Essentials 07 - Método GET parte 1

**SEPARANDO AS RESPONSABILIDADES EM UM PROJETO REAL, PACOTES:**

`controller` - é onde vai ter os `Endpoints`;

`domain` - representa o que temos do banco de dados;

`repository` - a conexão diretamente com o banco de dados, todas as query vão ficar dentro dessa interface;

`service` - é onde faz a lógica de negócios;

Cria pacote `service` e cria uma classe `xServide`- é a classe responsável pela regra de negócio, a regra de negócio não fica dentro de controller e sim no service;

Cria pacote `repository` - é onde representa o banco de dados;
