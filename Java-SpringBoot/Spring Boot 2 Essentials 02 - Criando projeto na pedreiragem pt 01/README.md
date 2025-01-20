## Spring Boot 2 Essentials 02 - Criando projeto na pedreiragem pt 01

`starter`- as dependências são gerenciadas pelo próprio spring;

`parent` - tem que criar como se fosse uma dependência; no parent as dependências serão herdadas da versão do parent;

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.1.4</version> <!-- Substitua pela versão do Spring Boot que deseja usar -->
    <relativePath/> <!-- Procura o parent em um repositório remoto, não local -->
</parent>
```

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

VSCODE -> Ctrl+Shift+P Spring Initializr: Generate a Maven Project

- Group: com.example (ou o que desejar)
- Artifact: nome do seu projeto

```
C:\Users\eduar>java -version
java version "17.0.13" 2024-10-15 LTS
Java(TM) SE Runtime Environment (build 17.0.13+10-LTS-268)
Java HotSpot(TM) 64-Bit Server VM (build 17.0.13+10-LTS-268, mixed mode, sharing)

C:\Users\eduar>mvn -version
Apache Maven 3.9.9 (8e8579a9e76f7d015ee5ec7bfcdc97d260186937)
Maven home: C:\eclipse\apache-maven-3.9.9
Java version: 17.0.13, vendor: Oracle Corporation, runtime: C:\Program Files\Java\jdk-17
Default locale: pt_BR, platform encoding: Cp1252
OS name: "windows 11", version: "10.0", arch: "amd64", family: "windows"
```
