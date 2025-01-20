## Method Reference pt 01 - Referência a métodos estáticos

`Method Reference` - é uma sintaxe para utilizar com parte das lambdas que simplificam ainda mais;

- uma regrinha que pode lembrar de forma fácil que se sua lambda chamar apenas um método você pode utilizar Method Reference;

```java
map(strings, (String s) -> s.lenght());
map(string, s -> s.toUpperCase());
```

```java
map(strings, String::lenght());
map(string, String::toUpperCase());
```

**`EXEMPLO CHATGPT`**

- **Method Reference** ela permite que você se refira diretamente a métodos, seja estáticos, de instância, ou construtores, de forma compacta e legível. Para entender isso, vamos começar com a referência a métodos estáticos.

```java
Classe::métodoEstático
```

**Exemplo: Usando referência a métodos estáticos**

- Vamos supor que você tenha um método estático chamado parseInt na classe Integer. Esse método converte uma String em um número inteiro.

```java
public class MethodReferenceExample {
    public static void main(String[] args) {
        // Exemplo sem referência a método
        Function<String, Integer> converter1 = s -> Integer.parseInt(s);
        System.out.println(converter1.apply("123")); // Saída: 123

        // Usando referência a método estático
        Function<String, Integer> converter2 = Integer::parseInt;
        System.out.println(converter2.apply("456")); // Saída: 456
    }
}
```

## Method Reference pt 02 - Referência a métodos não estáticos

**`EXEMPLO CHATGPT`**

Uma referência a métodos não estáticos em Java é usada para invocar métodos de instância de um objeto específico. Esses métodos exigem que um objeto esteja presente para que sejam chamados. Aqui está um exemplo simples para ilustrar:

Exemplo

Imaginemos que você tenha uma classe chamada Carro, onde deseja exibir informações sobre o carro usando uma referência a métodos não estáticos.

```java
import java.util.Arrays;
import java.util.List;

class Carro {
    private String modelo;

    public Carro(String modelo) {
        this.modelo = modelo;
    }

    public String getModelo() {
        return modelo;
    }

    public void imprimirModelo() {
        System.out.println("Modelo do carro: " + modelo);
    }
}

public class Main {
    public static void main(String[] args) {
        // Lista de objetos Carro
        List<Carro> carros = Arrays.asList(
            new Carro("Ferrari"),
            new Carro("Lamborghini"),
            new Carro("Porsche")
        );

        // Usando referência a métodos não estáticos
        carros.forEach(Carro::imprimirModelo);
    }
}
```

**`SEM REFERENCIA`**

```java
import java.util.Arrays;
import java.util.List;

class Carro {
    private String modelo;

    public Carro(String modelo) {
        this.modelo = modelo;
    }

    public String getModelo() {
        return modelo;
    }

    public void imprimirModelo() {
        System.out.println("Modelo do carro: " + modelo);
    }
}

public class Main {
    public static void main(String[] args) {
        // Lista de objetos Carro
        List<Carro> carros = Arrays.asList(
            new Carro("Ferrari"),
            new Carro("Lamborghini"),
            new Carro("Porsche")
        );

        // Usando expressão lambda em vez de referência a métodos
        carros.forEach(carro -> carro.imprimirModelo());
    }
}
```

**`SEM REFERENCIA, SEM LAMBDAS`**

```java
import java.util.Arrays;
import java.util.List;

class Carro {
    private String modelo;

    public Carro(String modelo) {
        this.modelo = modelo;
    }

    public String getModelo() {
        return modelo;
    }

    public void imprimirModelo() {
        System.out.println("Modelo do carro: " + modelo);
    }
}

public class Main {
    public static void main(String[] args) {
        // Lista de objetos Carro
        List<Carro> carros = Arrays.asList(
            new Carro("Ferrari"),
            new Carro("Lamborghini"),
            new Carro("Porsche")
        );

        // Usando um loop tradicional para chamar o método
        for (Carro carro : carros) {
            carro.imprimirModelo();
        }
    }
}
```

`BiPredicate` - é uma interface funcional que recebe dois argumentos e retorna um valor booleano, geralmente para avaliar alguma condição lógica.

```java
import java.util.function.BiPredicate;

public class Main {
    public static void main(String[] args) {
        // Criando um BiPredicate para verificar se a soma de dois números é maior que 10
        BiPredicate<Integer, Integer> isSomaMaiorQueDez = (num1, num2) -> (num1 + num2) > 10;

        // Testando o BiPredicate com diferentes valores
        System.out.println(isSomaMaiorQueDez.test(5, 6)); // true (5 + 6 = 11)
        System.out.println(isSomaMaiorQueDez.test(3, 4)); // false (3 + 4 = 7)
        System.out.println(isSomaMaiorQueDez.test(10, 1)); // true (10 + 1 = 11)
    }
}
```

## Method Reference pt 03 - Referência a construtor

**`EXEMPLO CHATGPT`**

A referência a construtor em Java é uma forma de simplificar a criação de objetos quando usada em combinação com interfaces funcionais que têm métodos de fábrica, como Supplier, Function, ou BiFunction. É representada pelo operador ClassName::new.

````java
import java.util.function.Function;

class Pessoa {
    private String nome;

    // Construtor
    public Pessoa(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }
}

public class Main {
    public static void main(String[] args) {
        // Usando referência a construtor para criar uma Pessoa
        Function<String, Pessoa> criarPessoa = Pessoa::new;

        // Criando uma instância de Pessoa
        Pessoa pessoa = criarPessoa.apply("João");

        // Exibindo o nome
        System.out.println("Nome da pessoa: " + pessoa.getNome());
    }
}
````
**`SEM REFERENCIA`**

````java
import java.util.function.Function;

class Pessoa {
    private String nome;

    // Construtor
    public Pessoa(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }
}

public class Main {
    public static void main(String[] args) {
        // Usando uma expressão lambda em vez de referência ao construtor
        Function<String, Pessoa> criarPessoa = nome -> new Pessoa(nome);

        // Criando uma instância de Pessoa
        Pessoa pessoa = criarPessoa.apply("João");

        // Exibindo o nome
        System.out.println("Nome da pessoa: " + pessoa.getNome());
    }
}
````
**`SEM LAMBDAS`**

````java
class Pessoa {
    private String nome;

    // Construtor
    public Pessoa(String nome) {
        this.nome = nome;
    }

    public String getNome() {
        return nome;
    }
}

public class Main {
    public static void main(String[] args) {
        // Criando uma instância de Pessoa diretamente
        Pessoa pessoa = new Pessoa("João");

        // Exibindo o nome
        System.out.println("Nome da pessoa: " + pessoa.getNome());
    }
}

````