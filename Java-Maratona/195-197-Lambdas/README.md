## Lambdas pt 01 - Predicate

`predicate` é uma interface funcional que recebe um tipo e retorna um boolenano;

`lambdas` - para elas funcionarem, elas precisam obrigatoriamente que a interface que você esteja trabalhando ou seja uma `interface funcional`- é uma interface onde você tem apenas um método abstrato;

A `lambda` tem o parametro e o corpo(a expressão que pode ou não retornar alguma coisa);

```java
@FunctionalInterface
public interface CarPredicate {
    //anonimos, funções, conciso
    boolean test(Car car);
    //(parametro) -> <expressão>
    //(Car car) -> car.getColor().equals("green");
}


```

Chama as lambdas de funções porque elas não estão atreladas a nenhuma classe, você tem uma lambda sendo executada diretamente dentro de um método mas ela técnicamente não esta ligada a uma classe;

Qualquer interface funcional você pode utilizar lambdas;

**`EXEMPLO CHATGPT`**

1. A Lambda é como uma função, só que compacta e sem nome.

Exemplo:

Uma função para verificar se um número é par:

```java
public boolean ehPar(int numero) {
    return numero % 2 == 0;
}
```

2. Quebrando isso em partes com Lambda:

Parâmetro(s):

O que a função recebe como entrada. No caso, o número (numero).
Em lambda: numero ->

Seta (->):

Separa os parâmetros da lógica da função.

Corpo da Função:

É a lógica que executa e retorna o resultado. No caso: numero % 2 == 0.

Lambda completa:

```java
numero -> numero % 2 == 0
```

## Lambdas pt 02 - Consumer

`consumer` é uma interface funcional, a diferença é que ele executa uma operação e não retorna nada;

**`EXEMPLO CHATGPT`**

Um Consumer em Java é uma interface funcional que aceita um único argumento e não retorna nada. Ele é útil quando você precisa executar uma ação com um dado, mas não precisa retornar nada.

Estrutura do Consumer

A assinatura do método funcional é:

```java
void accept(T t);
```

Onde:

- T é o tipo do dado que o Consumer aceita.
- O método accept é o que realiza alguma operação com o dado.

```java
import java.util.List;
import java.util.function.Consumer;

public class ExemploConsumer {
    public static void main(String[] args) {
        // Lista de nomes
        List<String> nomes = List.of("Alice", "Bob", "Carlos");

        // Criando um Consumer que imprime o nome
        Consumer<String> imprimirNome = nome -> System.out.println("Nome: " + nome);

        // Usando o Consumer em cada nome da lista
        nomes.forEach(imprimirNome);
    }
}

```

## Lambdas pt 03 - Function

`function` - é uma interface funcional onde vai ter o tipo T(receber) e o tipo R(o que vai voltar);

**`EXEMPLO CHATGPT`**

A interface funcional Function em Java é usada quando você precisa de uma função que:

Aceita um argumento de entrada.

Retorna um valor.

```java
R apply(T t);
```

T: O tipo do argumento de entrada.

R: O tipo do valor retornado.

```java
import java.util.function.Function;

public class ExemploFunction {
    public static void main(String[] args) {
        // Criando uma Function que dobra um número
        Function<Integer, Integer> dobrar = numero -> numero * 2;

        // Usando a Function
        int resultado = dobrar.apply(5);

        System.out.println("Resultado: " + resultado); // Saída: 10
    }
}
```
