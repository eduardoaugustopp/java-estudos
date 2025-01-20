## Streams pt 01 - Introduction pt 01

`Streams` - é uma forma que você tem de processamento de dados e que ajuda fazer as funcionalidades que você precisa em uma coleção de uma forma mais funcional, mais declarativa; é muito parecido com o que faz no banco de dados, imagina uma coleção de mangas e você quer filtrar os mangas por números, coisas que geralmente consegui fazer no banco de dados mas que para fazer antigamente no java era bem complicado;

**`EXEMPLO CHATGPT`**

Streams no Java são uma poderosa API introduzida no Java 8 que permite processar coleções de dados de maneira declarativa, como filtros, mapeamentos, reduções, e muito mais. Isso significa que você pode manipular e transformar dados de uma forma mais simples e elegante, muitas vezes evitando loops explícitos como for ou while.

Suponha que temos uma lista de números e queremos filtrar os números pares, dobrá-los, e depois somá-los.

**Código sem Streams:**

```java
public class Main {
    public static void main(String[] args) {
        List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6);
        List<Integer> paresDobro = new ArrayList<>();

        // Filtrar números pares e dobrar
        for (Integer numero : numeros) {
            if (numero % 2 == 0) { // Verifica se é par
                paresDobro.add(numero * 2); // Dobra o número
            }
        }

        // Somar todos os valores
        int soma = 0;
        for (Integer num : paresDobro) {
            soma += num;
        }

        System.out.println("Soma: " + soma);
    }
}

```

**Código com Streams:**

```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6);

        // Filtrar, dobrar e somar com Streams
        int soma = numeros.stream() //Cria um Stream a partir da lista numeros
                          .filter(n -> n % 2 == 0) // Filtra números pares
                          .map(n -> n * 2)        // Dobra o valor
                          .reduce(0, Integer::sum); // Soma os valores

        System.out.println("Soma: " + soma);
    }
}
```

**Outros Exemplos Práticos**

`Transformar uma lista de nomes em letras maiúsculas:`

```java
List<String> nomes = List.of("Alice", "Bob", "Carlos");
List<String> maiusculos = nomes.stream()
                               .map(String::toUpperCase)
                               .toList();
System.out.println(maiusculos); // [ALICE, BOB, CARLOS]
```

`Filtrar números maiores que 10 e listar:`

```java
List<Integer> numeros = List.of(5, 15, 20, 8, 9);
List<Integer> maioresQue10 = numeros.stream()
                                    .filter(n -> n > 10)
                                    .toList();
System.out.println(maioresQue10); // [15, 20]
```

`Contar quantos nomes têm mais de 3 letras:`

```java
List<String> nomes = List.of("Ana", "João", "Lu", "Carlos");
long count = nomes.stream()
                  .filter(nome -> nome.length() > 3)
                  .count();
System.out.println(count); // 2

```

## Streams pt 02 - Introduction pt 02

`streams` - uma sequência de elementos, esses elementos estão no tempo porque trabalha na sequência, coleções - elementos em um espaço, aonde esta aguardando a os dados coleções, falando de memória;

Temos 2 operações em streams:

Intermediárias: uma ação intermediária ela retorna o próprio stream ou seja você pode encadear ações, você pode filtrar e isso vai retornar uma outra stream;

Finais:

**`EXEMPLO CHATGPT`**

Operações intermediárias como filter(), map(), sorted(), e distinct() permitem que você transforme e manipule dados em Streams de maneira funcional e expressiva. Elas não são executadas imediatamente; o Stream é processado apenas quando uma operação terminal (como toList(), forEach(), reduce()) é chamada.

sorted(): Ordena os elementos do Stream.

```java
List<Integer> numeros = List.of(5, 3, 8, 1, 4);

List<Integer> ordenados = numeros.stream()
                                .sorted()    // Ordena os números
                                .toList();
System.out.println(ordenados);  // Saída: [1, 3, 4, 5, 8]
```

Operações Terminais são aquelas que consomem o Stream e produzem um resultado. Exemplos incluem forEach(), collect(), reduce(), count(), entre outros.

reduce(): Reduz os elementos a um único valor (exemplo de soma de números).

```java
List<Integer> numeros = List.of(1, 2, 3, 4, 5);
int soma = numeros.stream()
                  .reduce(0, (a, b) -> a + b);  // Soma os números
System.out.println("Soma: " + soma);  // Saída: 15

```

**Exemplo Completo com Ambas (Intermediária + Terminal)**

```java
import java.util.List;
import java.util.stream.Collectors;

public class Main {
    public static void main(String[] args) {
        List<Integer> numeros = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        // Operações intermediárias (filter e map) e operação terminal (collect)
        List<Integer> resultado = numeros.stream()
                                         .filter(n -> n % 2 == 0)  // Intermediária: Filtra os números pares
                                         .map(n -> n * 2)          // Intermediária: Dobra os números filtrados
                                         .collect(Collectors.toList());  // Terminal: Coleta em uma lista

        System.out.println(resultado);  // Saída: [4, 8, 12, 16, 20]
    }
}
```

## Streams pt 04 - FlatMap pt 01

`flatmap` - basicamente vai fazer a mesma coisa que map faz, a diferença é que o flatmap é utilizado quando você tem propriedades aninhadas, uma dentro da outra; ele é utilizado para esses casos aonde você tem que tirar um atributo que esta aninhado dentro de outro atributo e na verdade você esta falando em uma coleções de atributo;

**`EXEMPLO CHATGPT`**

`map():` Cada elemento do Stream é transformado em um único elemento no Stream resultante.

`flatMap():` Cada elemento é transformado em um Stream, e todos esses Streams são concatenados em um único Stream.

1. Exemplo Simples com flatMap()

Aqui temos uma lista que contém várias listas de números e queremos "achatar" todas as listas em uma única sequência de números.

```java
public class Main {
    public static void main(String[] args) {
        // Lista de listas de inteiros
        List<List<Integer>> listaDeListas = List.of(
            List.of(1, 2, 3),
            List.of(4, 5),
            List.of(6, 7, 8)
        );

        // Usando flatMap para achatar a lista de listas em uma lista de inteiros
        List<Integer> resultado = listaDeListas.stream()
                                                .flatMap(List::stream)  // Achata as listas em um único Stream
                                                .collect(Collectors.toList());  // Coleta os elementos em uma lista

        System.out.println(resultado);  // Saída: [1, 2, 3, 4, 5, 6, 7, 8]
    }
}
```

## Streams pt 06 - Finding and Matching

**`EXEMPLO CHATGPT`**

As operações de "Finding and Matching" com Streams permitem localizar elementos e verificar condições em coleções de dados. Aqui está uma visão geral das principais operações:

`anyMatch:` Verifica se algum elemento atende a uma condição.

`allMatch:` Verifica se todos os elementos atendem a uma condição.

`noneMatch:` Verifica se nenhum elemento atende a uma condição.

`findFirst:` Retorna o primeiro elemento da Stream.

`findAny:` Retorna qualquer elemento (útil em Streams paralelas).

```java
public class StreamFindingAndMatching {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(10, 15, 20, 25, 30);

        // anyMatch: Verifica se algum número é divisível por 5
        boolean anyDivisibleBy5 = numbers.stream()
                                         .anyMatch(n -> n % 5 == 0);
        System.out.println("Algum número é divisível por 5? " + anyDivisibleBy5);

        // allMatch: Verifica se todos os números são maiores que 5
        boolean allGreaterThan5 = numbers.stream()
                                         .allMatch(n -> n > 5);
        System.out.println("Todos os números são maiores que 5? " + allGreaterThan5);

        // noneMatch: Verifica se nenhum número é negativo
        boolean noneNegative = numbers.stream()
                                       .noneMatch(n -> n < 0);
        System.out.println("Nenhum número é negativo? " + noneNegative);

        // findFirst: Retorna o primeiro número da lista
        Optional<Integer> firstNumber = numbers.stream()
                                               .findFirst();
        firstNumber.ifPresent(n -> System.out.println("Primeiro número: " + n));

        // findAny: Retorna qualquer número da lista
        Optional<Integer> anyNumber = numbers.stream()
                                             .findAny();
        anyNumber.ifPresent(n -> System.out.println("Qualquer número encontrado: " + n));
    }
}

```

```
Algum número é divisível por 5? true
Todos os números são maiores que 5? true
Nenhum número é negativo? true
Primeiro número: 10
Qualquer número encontrado: 10
```

## Streams pt 07 - Reduce pt 01

`redude` - é uma operação terminal dos streams e vai possibilitar você um resultado único a partir de uma sequência de elementos, então ele vai repetidamente aplica a operação aos elementos e no final você vai ter um resultado;

**`EXEMPLO CHATGPT`**

`reduce` em Java Streams é usado para combinar elementos de uma coleção em um único resultado. Ele é uma das operações mais poderosas de Streams, permitindo realizar operações como somas, concatenações ou outras reduções personalizadas.

Operação reduce

O método possui três formas principais:

1. Com um acumulador e identidade:

Útil para fornecer um valor inicial (identidade).

Sempre retorna um valor, mesmo se a Stream estiver vazia.

2. Com um acumulador apenas:

Retorna um Optional, pois a Stream pode estar vazia.

3. Com acumulador e combinador:

Usado em Streams paralelas, onde combina resultados parciais.

Exemplo 1: Soma de Números (Acumulador e Identidade)

Expressão com Lambda:

```java
public class StreamReduceExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        // Soma usando reduce com identidade
        int sum = numbers.stream()
                         .reduce(0, (a, b) -> a + b);
        //0: Identidade (valor inicial).
        //(a, b) -> a + b: Função de acumulação, que soma dois números
        System.out.println("Soma dos números: " + sum);
    }
}

```

Expressão com Method Reference

```java
public class StreamReduceExample {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

        // Soma usando reduce com method reference
        int sum = numbers.stream()
                         .reduce(0, Integer::sum);

        System.out.println("Soma dos números: " + sum);
    }
}

```

## Streams pt 09 - Gerando streams pt 01

**`EXEMPLO CHATGPT`**

Exemplo 1: Gerando Streams a partir de uma lista

```java
public class StreamExample {
    public static void main(String[] args) {
        // Criando uma lista
        List<String> nomes = Arrays.asList("Eduardo", "Camila", "Lucas", "Ana");

        // Gerando um Stream a partir da lista
        Stream<String> streamDeNomes = nomes.stream();

        // Operação intermediária e terminal no Stream
        streamDeNomes
                .filter(nome -> nome.startsWith("C"))
                .forEach(System.out::println); // Resultado: Camila
    }
}

```

Exemplo 2: Gerando Streams com Stream.of

```java
public class StreamOfExample {
    public static void main(String[] args) {
        // Gerando um Stream diretamente
        Stream<String> stream = Stream.of("Maçã", "Banana", "Cereja", "Damasco");

        // Operação intermediária e terminal
        stream
                .filter(fruta -> fruta.length() > 5)
                .forEach(System.out::println); // Resultado: Banana, Cereja, Damasco
    }
}

```

Exemplo 3: Gerando Streams com números (range)

```java
public class IntStreamExample {
    public static void main(String[] args) {
        // Gerando um Stream de números
        IntStream.range(1, 5) // Intervalo de 1 a 4 (exclui o 5)
                .forEach(System.out::println); // Resultado: 1, 2, 3, 4
    }
}

```

## Streams pt 11 - Collectors pt 01 Summarizing

`collectors` - é como se fosse um framework dentro do streams porque ele possibilita você fazer varias coisas;

**`EXEMPLO CHATGPT`**

O Collectors é uma parte poderosa da API de Streams do Java, que permite a coleta de dados do Stream em várias formas. Uma das funcionalidades úteis é a capacidade de sumarizar dados utilizando o Collectors.summarizingInt, Collectors.summarizingDouble, ou Collectors.summarizingLong. Esses métodos fornecem um resumo estatístico, incluindo soma, média, valor mínimo, valor máximo e contagem.

Aqui está um exemplo para ilustrar como usar Collectors.summarizingInt para sumarizar dados de um Stream:

```java
public class CollectorsSummarizingExample {
    public static void main(String[] args) {
        // Lista de números
        List<Integer> numeros = Arrays.asList(10, 20, 30, 40, 50);

        // Coletando estatísticas usando summarizingInt
        IntSummaryStatistics estatisticas = numeros.stream()//Stream da lista: Um Stream foi criado a partir da lista de números.
                .collect(Collectors.summarizingInt(Integer::intValue));//Collectors.summarizingInt: Esse método gera um objeto IntSummaryStatistics que contém os dados estatísticos.

        // Exibindo os resultados
        System.out.println("Contagem: " + estatisticas.getCount());      // Total de elementos
        System.out.println("Soma: " + estatisticas.getSum());           // Soma de todos os números
        System.out.println("Média: " + estatisticas.getAverage());      // Média dos valores
        System.out.println("Valor mínimo: " + estatisticas.getMin());   // Menor número
        System.out.println("Valor máximo: " + estatisticas.getMax());   // Maior número
    }
}

```

```
Contagem: 5
Soma: 150
Média: 30.0
Valor mínimo: 10
Valor máximo: 50

```

## Streams pt 12 - Collectors pt 02 - Grouping by pt 01

**`EXEMPLO CHATGPT`**

O método Collectors.groupingBy é usado para agrupar elementos de um Stream com base em uma função classificadora. Ele retorna um mapa onde as chaves representam os grupos e os valores são as listas de elementos que pertencem a cada grupo.

Exemplo: Agrupando por Comprimento das Palavras

```java
public class GroupingByExample {
    public static void main(String[] args) {
        // Lista de palavras
        List<String> palavras = Arrays.asList("banana", "maçã", "laranja", "uva", "pêssego", "caju");

        // Agrupando as palavras pelo comprimento
        Map<Integer, List<String>> palavrasAgrupadasPorTamanho = palavras.stream()//Stream da lista: Um Stream é criado a partir da lista de palavras.
                .collect(Collectors.groupingBy(String::length));

        // Exibindo o resultado
        palavrasAgrupadasPorTamanho.forEach((tamanho, grupo) -> {
            System.out.println("Tamanho: " + tamanho);
            System.out.println("Palavras: " + grupo);
        });
    }
}

```

```
Tamanho: 6
Palavras: [banana, laranja, pêssego]
Tamanho: 4
Palavras: [maçã, uva, caju]

```

Exemplo 2: Agrupando por Inicial da Palavra

```java
public class GroupingByInitialExample {
    public static void main(String[] args) {
        // Lista de palavras
        List<String> palavras = Arrays.asList("banana", "maçã", "laranja", "uva", "pêssego", "caju");

        // Gerando um mapa que agrupa palavras pela letra inicial
        Map<Character, List<String>> palavrasAgrupadasPorInicial = palavras.stream()
                .collect(Collectors.groupingBy(palavra -> palavra.charAt(0))); // Agrupa pela função classificadora, que aqui é a letra inicial da palavra.

        // Itera sobre o mapa gerado e exibe cada grupo
        palavrasAgrupadasPorInicial.forEach((inicial, grupo) -> { // Para cada entrada (inicial e grupo de palavras):
            System.out.println("Inicial: " + inicial); // Exibe a chave do grupo (letra inicial das palavras).
            System.out.println("Palavras: " + grupo);  // Exibe a lista de palavras que começam com essa inicial.
        });
    }
}

```

```
Inicial: b
Palavras: [banana]
Inicial: m
Palavras: [maçã]
Inicial: l
Palavras: [laranja]
Inicial: u
Palavras: [uva]
Inicial: p
Palavras: [pêssego]
Inicial: c
Palavras: [caju]

```

## Streams pt 16 - Parallel Streams pt 01

`parallel` - basicamente funciona assim você tem o computador que nós chamamos de processador, processador ele geralmente tem os núcleos e dentro dos nucleos temos o que nós chamamos de threads, as threads são como se fosse trabalhadores, então imagina que tem uma pessoa que fica sempre trabalhando fazendo calculo, fazendo todo o processamento das informações que você tem, então quanto mais threads voce tem maior a capacidade de processamento paralelo que voce tem dentro do seu computador;

**`EXEMPLO CHATGPT`**

Os Parallel Streams permitem processar elementos de um Stream em paralelo, utilizando múltiplos núcleos do processador para melhorar a performance em operações que envolvem grandes quantidades de dados ou tarefas computacionalmente intensas. Isso é obtido com o método parallelStream() ou transformando um Stream sequencial em paralelo com parallel().

Exemplo: Processando Números com Parallel Streams

```java
public class ParallelStreamExample {
    public static void main(String[] args) {
        // Lista de números
        List<Integer> numeros = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        // Processando os números em paralelo
        numeros.parallelStream() // Cria um Parallel Stream a partir da lista.
                .forEach(numero -> { // Executa a operação para cada elemento da lista.
                    System.out.println("Thread: " + Thread.currentThread().getName() + " | Número: " + numero);
                });
        // A ordem dos números no output pode variar devido ao processamento paralelo.
    }
}
```

```
Thread: ForkJoinPool.commonPool-worker-3 | Número: 3
Thread: ForkJoinPool.commonPool-worker-5 | Número: 5
Thread: ForkJoinPool.commonPool-worker-7 | Número: 7
Thread: main | Número: 1
Thread: ForkJoinPool.commonPool-worker-4 | Número: 4
...

```
Exemplo 2: Paralelismo com Operação de Soma

````java
public class ParallelSumExample {
    public static void main(String[] args) {
        // Criando um range de números de 1 a 1.000.000
        int soma = IntStream.rangeClosed(1, 1_000_000) // Cria um Stream de 1 a 1.000.000.
                .parallel() // Transforma o Stream em paralelo.
                .sum(); // Calcula a soma de todos os números.

        System.out.println("Soma: " + soma); // Exibe a soma.
    }
}
````