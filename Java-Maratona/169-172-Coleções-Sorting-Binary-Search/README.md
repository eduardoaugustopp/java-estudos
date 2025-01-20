## Coleções pt 09 - Sorting lists pt 01

```java
List<String> nomes = new ArrayList<>();
        nomes.add("Jose");
        nomes.add("Luci");
        nomes.add("DevDojo");
        nomes.add("Breno");
        nomes.add("Caio");
        nomes.add(0, "Anna");

        Collections.sort(nomes);

        List<Double> numeros = new ArrayList<>();
        numeros.add(1.6);
        numeros.add(1.1);
        numeros.add(0.6);
        numeros.add(1.0);
        numeros.add(2d);

        Collections.sort(numeros);

        for(String nome: nomes){
            System.out.println(nome);
        }

        for(Double numero : numeros){
            System.out.println(numero);
        }
```

```
Anna
Breno
Caio
DevDojo
Jose
Luci
0.6
1.0
1.1
1.6
2.0
```

Para classes wrapper (como Integer, Double): Use `Objects.requireNonNull` apenas quando o valor não pode ser nulo no contexto do seu design. Caso contrário, defina claramente o comportamento esperado para valores null.

## Coleções pt 10 - Sorting lists pt 02 - Comparable

**Se você estivesse usando a classe String, não precisaria implementar Comparable, porque String já implementa a interface Comparable na API padrão do Java.**

**`EXEMPLO CHATGPT`**

A interface `Comparable` é usada para definir a ordem natural dos objetos de uma classe.

**Exemplo: Ordenando uma lista de Pessoa pela idade**

```java
class Pessoa implements Comparable<Pessoa> {
    private String nome;
    private int idade;

    public Pessoa(String nome, int idade) {
        this.nome = nome;
        this.idade = idade;
    }

    public String getNome() {
        return nome;
    }

    public int getIdade() {
        return idade;
    }

    @Override
    public int compareTo(Pessoa outraPessoa) {
        // Ordenação crescente por idade
        return Integer.compare(this.idade, outraPessoa.idade);
    }

    @Override
    public String toString() {
        return nome + " (" + idade + " anos)";
    }
}

public class Main {
    public static void main(String[] args) {
        List<Pessoa> pessoas = new ArrayList<>();
        pessoas.add(new Pessoa("Ana", 25));
        pessoas.add(new Pessoa("Carlos", 20));
        pessoas.add(new Pessoa("Bruno", 30));

        System.out.println("Antes da ordenação:");
        System.out.println(pessoas);

        // Ordena a lista usando a ordem natural definida no método compareTo
        Collections.sort(pessoas);

        System.out.println("\nDepois da ordenação:");
        System.out.println(pessoas);
    }
}
```

```
Antes da ordenação:
[Ana (25 anos), Carlos (20 anos), Bruno (30 anos)]

Depois da ordenação:
[Carlos (20 anos), Ana (25 anos), Bruno (30 anos)]
```

- Implementa a interface `Comparable` e coloca o tipo `<>`, ai coloca o unico método que tem `compareTo` - esse método retorna um inteiro, voce tem acesso a dois objetos um objeto `this` que é o que esta executando esse compareTo e tem o outro objeto que esta sendo passado como variavel de referencia como argumento `(tipo x)`;

```java
public class Produto implements Comparable<Produto> {

    private String serialNumero;
    private String nome;
    private Double preco;


    public Produto(String serialNumero, String nome, Double preco) {
        this.serialNumero = serialNumero;
        this.nome = nome;
        this.preco = preco;
    }


    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Produto produto = (Produto) o;
        return Objects.equals(serialNumero, produto.serialNumero);
    }

    @Override
    public int hashCode() {
        return Objects.hash(serialNumero);
    }

    @Override
    public String toString() {
        return "Produto{" +
                "serialNumero='" + serialNumero + '\'' +
                ", nome='" + nome + '\'' +
                ", preco=" + preco +
                '}';
    }

    public String getSerialNumero() {
        return serialNumero;
    }

    public void setSerialNumero(String serialNumero) {
        this.serialNumero = serialNumero;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public Double getPreco() {
        return preco;
    }

    public void setPreco(Double preco) {
        this.preco = preco;
    }


    @Override
    public int compareTo(Produto o) {

        Double d = preco;

        //return this.serialNumero.compareTo(o.getSerialNumero());
        return d.compareTo(o.getPreco());
    }
}

```

```java
public class SortProdutoTest {
    public static void main(String[] args) {

        List<Produto> produtos = new ArrayList<>();
        Produto produto1 = new Produto("123", "Laptop Lenovo", 2000.0);
        Produto produto2 = new Produto("321", "Picanha", 26.4);
        Produto produto3 = new Produto("879", "Teclado Razer", 1000.0);
        Produto produto4 = new Produto("012", "Samsung galaxy S7 64Gb", 3250.5);

        produtos.add(produto1);
        produtos.add(produto2);
        produtos.add(produto3);
        produtos.add(produto4);

        Collections.sort(produtos);

        for(Produto produto: produtos){
            System.out.println(produto);
        }

    }
}

```

## Coleções pt 11 - Sorting lists pt 03 - Comparator

**`EXEMPLO CHATGPT`**

- O exemplo utiliza uma lista de objetos que são comparados com base em um de seus atributos:

**Exemplo: Comparar objetos com Comparator**

```java
class Pessoa {
    private String nome;
    private int idade;

    public Pessoa(String nome, int idade) {
        this.nome = nome;
        this.idade = idade;
    }

    public String getNome() {
        return nome;
    }

    public int getIdade() {
        return idade;
    }

    @Override
    public String toString() {
        return nome + " (" + idade + " anos)";
    }
}

public class ExemploComparator {
    public static void main(String[] args) {
        List<Pessoa> pessoas = new ArrayList<>();
        pessoas.add(new Pessoa("João", 25));
        pessoas.add(new Pessoa("Maria", 30));
        pessoas.add(new Pessoa("Ana", 22));

        // Ordenar pela idade
        Collections.sort(pessoas, new Comparator<Pessoa>() {
            @Override
            public int compare(Pessoa p1, Pessoa p2) {
                return Integer.compare(p1.getIdade(), p2.getIdade());
            }
        });

        System.out.println("Ordenado por idade:");
        for (Pessoa pessoa : pessoas) {
            System.out.println(pessoa);
        }

        // Ordenar pelo nome
        pessoas.sort(new Comparator<Pessoa>() {
            @Override
            public int compare(Pessoa p1, Pessoa p2) {
                return p1.getNome().compareTo(p2.getNome());
            }
        });

        System.out.println("\nOrdenado por nome:");
        for (Pessoa pessoa : pessoas) {
            System.out.println(pessoa);
        }
    }
}
```

```
Ordenado por idade:
Ana (22 anos)
João (25 anos)
Maria (30 anos)

Ordenado por nome:
Ana (22 anos)
João (25 anos)
Maria (30 anos)
```

`comparator` - é uma interface que tambem vai fazer a comparação a diferença é que essa interface tem o método `compare` e não utiliza implementando na sua classe diretamente;

```java
class ProdutoNomeComparator implements Comparator<Produto>{


    @Override
    public int compare(Produto o1, Produto o2) {
        return o1.getNome().compareTo(o2.getNome());
    }
}

public class SortProdutoTest {
    public static void main(String[] args) {

        List<Produto> produtos = new ArrayList<>();

        Produto[] produtoArrays = new Produto[4];

        Produto produto1 = new Produto("123", "Laptop Lenovo", 2000.0);
        Produto produto2 = new Produto("321", "Picanha", 26.4);
        Produto produto3 = new Produto("879", "Teclado Razer", 1000.0);
        Produto produto4 = new Produto("012", "Samsung galaxy S7 64Gb", 3250.5);

        produtos.add(produto1);
        produtos.add(produto2);
        produtos.add(produto3);
        produtos.add(produto4);

        produtoArrays[0] = produto1;
        produtoArrays[1] = produto2;
        produtoArrays[2] = produto3;
        produtoArrays[3] = produto4;

        Collections.sort(produtos);

        for(Produto produto: produtos){
            System.out.println(produto);
        }
        System.out.println("--------------------------");


        Arrays.sort(produtoArrays, new ProdutoNomeComparator());
        for (Produto produto : produtoArrays){
            System.out.println(produto);
        }


    }
}
```

## Coleções pt 12 - Binary Search

`Binary Search` - outra forma que temos de fazer busca em um array, a diferença para o `indexOf` é o `Binary Search` retorna a posição que você deveria inserir esse elemento caso não exista;

// (-(ponto de inserção) -1)
// index 0, 1, 2, 3, 4
// value 10, 20, 30, 40, 50

**`EXEMPLO CHATGPT`**

- O método Collections.binarySearch é utilizado para realizar uma busca binária em uma lista. Essa operação é eficiente, com complexidade de tempo 𝑂 ( log ⁡ 𝑛 ) O(logn), mas para que funcione corretamente, a lista deve estar ordenada.

```java
public class ExemploBinarySearch {
    public static void main(String[] args) {
        // Criando uma lista de números
        List<Integer> numeros = new ArrayList<>();
        numeros.add(10);
        numeros.add(20);
        numeros.add(30);
        numeros.add(40);
        numeros.add(50);

        // Ordenar a lista (necessário para binarySearch)
        Collections.sort(numeros);

        // Buscar um número na lista
        int index = Collections.binarySearch(numeros, 30);

        if (index >= 0) {
            System.out.println("Número 30 encontrado no índice: " + index);
        } else {
            System.out.println("Número 30 não encontrado.");
        }

        // Tentar buscar um número que não está na lista
        int indexInexistente = Collections.binarySearch(numeros, 25);

        if (indexInexistente >= 0) {
            System.out.println("Número 25 encontrado no índice: " + indexInexistente);
        } else {
            System.out.println("Número 25 não encontrado. Posição sugerida: " + (-indexInexistente - 1));
        }
    }
}

```

```
Número 30 encontrado no índice: 2
Número 25 não encontrado. Posição sugerida: 2
```

Lista Ordenada: Para que o método binarySearch funcione, a lista precisa estar ordenada. Caso contrário, o resultado será imprevisível.

Busca Binária:

- Se o elemento for encontrado, o índice é retornado.

- Se o elemento não for encontrado, o retorno será negativo.

- A posição onde o elemento poderia ser inserido na lista é obtida com o cálculo -(retorno) - 1.

```java
package br.com.abc.javacore.colecoes.testes;

import br.com.abc.javacore.colecoes.classes.Produto;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class BinarySearchTest {
    public static void main(String[] args) {
        List<Integer> numeros = new ArrayList<>();
        numeros.add(2);
        numeros.add(0);
        numeros.add(4);
        numeros.add(3);

        // (-(ponto de insersão) -1), -1, -1 = -2
        Collections.sort(numeros);
        // 0,1,2,3
        // 0,2,3,4
        System.out.println(Collections.binarySearch(numeros,1));

        List<Produto> produtos = new ArrayList<>();
        Produto produto1 = new Produto("123", "Laptop Lenovo", 2000.0);
        Produto produto2 = new Produto("321", "Picanha", 26.4);
        Produto produto3 = new Produto("879", "Teclado Razer", 1000.0);
        Produto produto4 = new Produto("012", "Samsung galaxy S7 64Gb", 3250.5);
        produtos.add(produto1);
        produtos.add(produto2);
        produtos.add(produto3);
        produtos.add(produto4);

        Collections.sort(produtos, new ProdutoNomeComparator());

        Produto produto5 = new Produto("000", "Antena", 50.0);

        for (Produto produto : produtos) {
            System.out.println(produto);
        }

        System.out.println(Collections.binarySearch(produtos, produto5, new ProdutoNomeComparator()));

        Integer[] arrayInteger = new Integer[4];
        arrayInteger[0] = 2;
        arrayInteger[1] = 0;
        arrayInteger[2] = 4;
        arrayInteger[3] = 3;
        Arrays.sort(arrayInteger);
        System.out.println(Arrays.binarySearch(arrayInteger,1));


    }
}

```

```
-2
Produto{serialNumero='123', nome='Laptop Lenovo', preco=2000.0}
Produto{serialNumero='321', nome='Picanha', preco=26.4}
Produto{serialNumero='012', nome='Samsung galaxy S7 64Gb', preco=3250.5}
Produto{serialNumero='879', nome='Teclado Razer', preco=1000.0}
-1
-2
```
