## Spring Boot 2 Essentials 11 - Método PUT

`put` - altera; quando faz o put você esta substituindo o estado inteiro do objeto;

- Retorna o `NO_CONTENT` porque você já tem os dados que você precisa, diretamente na sua requisição;

```java
     @PutMapping
    public ResponseEntity<Void> replace(@RequestBody Prata prata) {
        prataService.replace(prata);
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
```

```java
public void replace(Prata prata) {
        delete(prata.getId());
        pratas.add(prata);
    }
```
