## Spring Boot 2 Essentials 15 - Framework de Mapeamento MapStruct

https://mapstruct.org/documentation/installation/

```xml
<dependency>
    <groupId>org.mapstruct</groupId>
    <artifactId>mapstruct</artifactId>
    <version>1.5.2.Final</version>
</dependency>
```

**<org.mapstruct.version>1.6.3</org.mapstruct.version>**

```xml
<properties>
    <java.version>17</java.version>
    <org.mapstruct.version>1.6.3</org.mapstruct.version>
</properties>
```

Coloca no build:

```xml
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-compiler-plugin</artifactId>
<version>3.8.1</version>
<configuration>
    <release>17</release>
    <annotationProcessorPaths>
        <path>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct-processor</artifactId>
            <version>${org.mapstruct.version}</version>
        </path>
        <path>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </path>
    </annotationProcessorPaths>
</configuration>
</plugin>
```

Cria um pacote chamado `mapper`;

```java
package br.com.pkpratas01.mapper;

// A anotação @Mapper indica que esta interface será usada para mapeamento entre objetos.
import org.mapstruct.Mapper;
import org.mapstruct.factory.Mappers;

import br.com.pkpratas01.domain.Prata; // Classe que representa o domínio do objeto 'Prata'.
import br.com.pkpratas01.request.PrataPostRequestBody; // Classe que representa o corpo da requisição para criar uma nova 'Prata'.
import br.com.pkpratas01.request.PrataPutRequestBody; // Classe que representa o corpo da requisição para atualizar uma 'Prata'.

// A anotação @Mapper com o parâmetro componentModel="spring" faz com que o MapStruct crie uma implementação dessa interface como um bean do Spring.
@Mapper(componentModel="spring")
public abstract class PrataMapper {

    // O MapStruct gera uma implementação para esta interface, que será usada para realizar o mapeamento entre objetos.
    public static final PrataMapper INSTANCE = Mappers.getMapper(PrataMapper.class);

    // Método que converte o objeto PrataPostRequestBody para o objeto Prata.
    public abstract Prata toPrata(PrataPostRequestBody prataPostRequestBody);

    // Método que converte o objeto PrataPutRequestBody para o objeto Prata.
    public abstract Prata toPrata(PrataPutRequestBody prataPutRequestBody);
}
```

**`PACOTE SERVICE:`**

```java
public Prata save(PrataPostRequestBody prataPostRequestBody) {
    // O 'PrataPostRequestBody' é um objeto que contém os dados da requisição para criar uma nova 'Prata'.

    // Usando o 'PrataMapper' para converter o 'PrataPostRequestBody' para o objeto 'Prata'.
    // O 'toPrata' é um método do MapStruct, que converte um DTO em um objeto de domínio (Prata).
    Prata prata = PrataMapper.INSTANCE.toPrata(prataPostRequestBody);

    // Salvando o objeto 'Prata' no banco de dados usando o repositório.
    // O 'prataRepository.save' persiste a 'Prata' no banco, e retorna o objeto com seus dados atualizados (por exemplo, com o ID gerado).
    return prataRepository.save(prata);
}
```

```java
public void replace(PrataPutRequestBody prataPutRequestBody) {
    // Procurando o objeto 'Prata' existente no banco de dados com o ID fornecido no 'prataPutRequestBody'.
    // A função 'findByIdOrThrowBadRequestException' verifica se o ID existe, e lança uma exceção caso não encontre.
    Prata savedPrata = findByIdOrThrowBadRequestException(prataPutRequestBody.getId());

    // Convertendo o 'PrataPutRequestBody' para o objeto 'Prata', utilizando o 'PrataMapper'.
    // 'toPrata' converte os dados da requisição para o objeto de domínio 'Prata'.
    Prata prata = PrataMapper.INSTANCE.toPrata(prataPutRequestBody);

    // Atualizando o ID da 'Prata' convertida, para garantir que o ID do banco de dados seja preservado.
    prata.setId(savedPrata.getId());

    // Salvando a 'Prata' atualizada no banco de dados. Esse método substitui o objeto existente.
    prataRepository.save(prata);
}
```
