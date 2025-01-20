## Pattern Matching for instanceof

**`EXEMPLO CHATGPT`**

O Pattern Matching for instanceof é um recurso introduzido no Java 16, que facilita o uso do operador instanceof ao permitir a combinação da verificação de tipo com a declaração de uma variável, tudo em uma única linha de código. Isso simplifica o código, eliminando a necessidade de realizar o cast explicitamente.

Aqui está um exemplo simples:

Antes do Pattern Matching:

```java
if (obj instanceof String) {
    String s = (String) obj; // Cast explícito necessário
    System.out.println(s.length());
}
```

Com Pattern Matching (a partir do Java 16):

```java
if (obj instanceof String s) {
    System.out.println(s.length()); // Não precisa de cast explícito
}
```
