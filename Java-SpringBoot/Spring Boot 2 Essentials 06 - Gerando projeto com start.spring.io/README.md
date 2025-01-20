## Spring Boot 2 Essentials 06 - Gerando projeto com start.spring.io

**Passo a Passo para Criar e Executar um Projeto Spring Boot no VSCode**

1. **Acessar o Spring Initializr**
   Abra o navegador e acesse https://start.spring.io.
2. **Configuração do Projeto**

- **Project**: Selecione Maven Project (ou Gradle Project, caso preferir Gradle).

- **Language**: Selecione Java.

- **Spring Boot Version**: Escolha a versão do Spring Boot 2.x.

**Project Metadata**:

- **Group**: com.exemplo (ou o seu grupo de projeto).
- **Artifact**: meu-projeto (nome do seu artefato).
- **Name**: Nome do seu projeto (geralmente o mesmo do artifact).
- **Description**: Adicione uma breve descrição (ex: "Projeto exemplo de Spring Boot").
- **Package Name**: Nome do pacote (geralmente com.exemplo.meuprojeto).
- **Packaging**: Jar (padrão para a maioria dos projetos Spring Boot).
- **Java Version**: Escolha a versão do Java (ex: 11 ou 17).

**Dependencies**: Clique em "Add Dependencies" e adicione as dependências que você precisar, como:

- **Spring Web** (para APIs RESTful).
- **Spring Data JPA** (para trabalhar com banco de dados).
- **Thymeleaf** (para templates HTML).
- **H2 Database** (para um banco de dados em memória simples, por exemplo).

Após configurar, clique em **Generate** para baixar o arquivo `.zip` do projeto.

3. **Extrair o Arquivo ZIP**
   Extraia o arquivo .zip para uma pasta local no seu computador.
4. **Abrir o Projeto no VSCode**

Abra o VSCode.

No VSCode, clique em File > Open Folder....

Selecione a pasta onde você extraiu o arquivo .zip.

5. **Instalar as Extensões no VSCode**

Se você ainda não tem as extensões necessárias instaladas, siga estas etapas:

**Java Extension Pack**: Acesse a barra lateral esquerda do VSCode e clique na aba de Extensões (ícone de quadrado). Pesquise por "Java Extension Pack" e instale. Essa extensão inclui:

**Debugger for Java**

**Java Dependency Viewer**

**Spring Boot Tools**

Após instalar, reinicie o VSCode se necessário.

6. **Verificar a Detecção do Projeto**

- O VSCode deve detectar automaticamente que o projeto é um projeto Maven (ou Gradle), configurando as dependências.
- Se o VSCode não detectar automaticamente, você pode abrir o terminal e rodar:

```bash
mvn clean install
```

Esse comando baixa as dependências do Maven e compila o projeto.

7. **Executar o Projeto**

- Abra o terminal no VSCode. Para isso, vá em Terminal > New Terminal ou use o atalho Ctrl + `.
- Navegue até o diretório onde está o arquivo pom.xml (se estiver na pasta do projeto, isso já estará configurado automaticamente).
- Execute o seguinte comando no terminal:

```bash
mvn spring-boot:run
```

8. **Verificar a Execução**

- O Spring Boot iniciará a aplicação e você verá mensagens no terminal confirmando que o aplicativo está sendo executado.
- Acesse a aplicação no navegador em http://localhost:8080 ou a porta que você configurou.
