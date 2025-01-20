## Record Class

`Record Class` - é uma classe que não preciso usar lombok;

**`EXEMPLO CHATGPT`**

A record class em Java é um tipo especial de classe introduzido no Java 14 (como uma funcionalidade preview) e estabilizado no Java 16. O principal objetivo de uma record é fornecer uma maneira concisa e imutável de representar dados. Em outras palavras, ela é uma forma simplificada de criar classes que contêm apenas dados e têm um comportamento bastante padrão para acessar, comparar e imprimir esses dados.

Características de um record:

- Imutabilidade: Uma vez criado, os valores dos campos de um record não podem ser alterados.
- Conciso: O compilador automaticamente gera código para os métodos toString(), equals(), hashCode(), e um construtor.
- Tipicamente usado para representar dados imutáveis.

Exemplo de record:

```java
public record Car(String model, int year) {
}
```

Neste exemplo:

- Car é o nome do record.
- Ele possui dois campos: model (uma String) e year (um int).
- Não é necessário escrever um construtor explícito, os campos são automaticamente passados ao criar um novo objeto Car.

```java
public class Main {
    public static void main(String[] args) {
        Car car = new Car("Ferrari", 2025);
        System.out.println(car.model());  // Acessa o campo 'model'
        System.out.println(car.year());   // Acessa o campo 'year'
        System.out.println(car);          // Chama automaticamente o método toString()
    }
}
```
````
Ferrari
2025
Car[model=Ferrari, year=2025]
````