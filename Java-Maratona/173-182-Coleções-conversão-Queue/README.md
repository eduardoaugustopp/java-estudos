## Conversão de Lista para Arrays e vice versa

```java
public class ConversaoDeListasArraysTest {
    public static void main(String[] args) {

        List<Integer> numeros = new ArrayList<>();
        numeros.add(9);
        numeros.add(0);
        numeros.add(5);
        numeros.add(1);
        numeros.add(4);
        numeros.add(2);
        numeros.add(3);

        Integer[] numerosArrays = new Integer[numeros.size()];
        numeros.toArray(numerosArrays);
        System.out.println(numeros);
        System.out.println(Arrays.toString(numerosArrays));
        System.out.println("----------------------");

        Integer[] numerosArrys2 = new Integer[4];
        numerosArrys2[0] = 9;
        numerosArrys2[1] = 3;
        numerosArrys2[2] = 4;
        numerosArrys2[3] = 0;

        List<Integer> numeros2 = Arrays.asList(numerosArrys2);
        System.out.println(numeros2);

        List<Integer> numeros3 = new ArrayList<>();
        numeros3.addAll(Arrays.asList(numerosArrys2));
        System.out.println(numeros3);

        numeros2.set(0,1);
        numeros3.add(10);

        System.out.println(numeros2);
        System.out.println(numeros3);

    }
}

```

```
[9, 0, 5, 1, 4, 2, 3]
[9, 0, 5, 1, 4, 2, 3]
[9, 3, 4, 0]
[9, 3, 4, 0]
[1, 3, 4, 0]
[9, 3, 4, 0, 10]
```

## Coleções pt 14 - Iterator

`iterator` - é uma classe que ela meio que checa antes de fazer alguma ação;

```java
public class IteratorTest {
    public static void main(String[] args) {

        List<Produto> produtos = new LinkedList<>();

        Produto produto1 = new Produto("123", "Laptop Lenovo", 2000.0, 10);
        Produto produto2 = new Produto("321", "Picanha", 10d, 10);
        Produto produto3 = new Produto("879", "Teclado Razer", 1000.0, 0);
        Produto produto4 = new Produto("012", "Samsung galaxy S7 64Gb", 3250.5, 0);
        produtos.add(produto1);
        produtos.add(produto2);
        produtos.add(produto3);
        produtos.add(produto4);

        Iterator<Produto> produtoIterator = produtos.iterator();
        while(produtoIterator.hasNext()){//enquanto estiver; next pega o objeto;
            if(produtoIterator.next().getQuantidade() == 0){
                produtoIterator.remove();
            }
        }

        System.out.println(produtos);
        System.out.println(produtos.size());

    }
}

```

```
[Produto{serialNumero='123', nome='Laptop Lenovo', preco=2000.0, quantidade=10},
 Produto{serialNumero='321', nome='Picanha', preco=10.0, quantidade=10}]
2
```

## Coleções pt 15 - Set, HashSet

`LinkedList` - a vantagem de trabalhar orientada a objeto nós estavamos utilizando `ArrayList` mas existe uma outra opção que é o `LinkedList` - nada mais que um array onde cada uma das posições do array sabe sobre a posição anterior e a próxima; a vantagem de usar o LinkedList é a performance ele é melhor para remover, isso se for fazer várias remoções;

`set` - o set é uma interface, essa interface é uma collection, a vantagem do set é que ele não permite elementos duplicados dentro da coleção, `HashSet` - `hash` significa que essa é uma coleção aonde permite apenas elementos únicos e esses elementos eles serão organizados pelo Hash; `não existe indice` porque o set não é indexado; precisa navegar pela coleção usando for;

```java
public class SetTest {
    public static void main(String[] args) {
        Produto produto1 = new Produto("123", "Laptop Lenovo", 2000.0, 10);
        Produto produto2 = new Produto("321", "Picanha", 10d, 10);
        Produto produto3 = new Produto("879", "Teclado Razer", 1000.0, 0);
        Produto produto4 = new Produto("012", "Samsung galaxy S7 64Gb", 3250.5, 0);
        Produto produto5 = new Produto("012", "Samsung galaxy S6 64Gb", 3250.5, 0);
        Set<Produto> produtoSet = new HashSet<>(); // não matem a ordem
        //Set<Produto> produtoSet = new LinkedHashSet<>(); // matem a ordem
        produtoSet.add(produto1);
        produtoSet.add(produto2);
        produtoSet.add(produto3);
        produtoSet.add(produto4);
        produtoSet.add(produto5);
        for(Produto p : produtoSet){
            System.out.println(p);
        }
    }
}

```

```
Produto{serialNumero='012', nome='Samsung galaxy S7 64Gb', preco=3250.5, quantidade=0}
Produto{serialNumero='123', nome='Laptop Lenovo', preco=2000.0, quantidade=10}
Produto{serialNumero='321', nome='Picanha', preco=10.0, quantidade=10}
Produto{serialNumero='879', nome='Teclado Razer', preco=1000.0, quantidade=0}
```

## Coleções pt 16 - NavigableSet, TreeSet pt 01

`NavigableSet` - é uma interface que é extends de SortedSet e SortedSet é extends de Set; adiciona alguns métodos que você pode trabalhar para você pegar elementos baseado em posições existentes;

`TreeSet` - utiliza quando a classe que você esta criando a coleção implementa comparable ou caso não tenha precisa ter certeza de estar criando Comparator e tem que lembrar que toda vez que inserir um elemento o próprio TeeSet vai reordenar a coleção baseado no valor do compareTo;

**`As classes que ver com o nome "TREE" geralmente vão trabalhar diretamente linkado a parte de você ter o sort ou seja elas precisam que as classes que você esta adicionando dentro dessa coleção Tree você precisa ter certeza que elas tem comparable`**

```java
public class TreeSetTest {
    public static void main(String[] args) {
        Produto produto1 = new Produto("123", "Laptop Lenovo", 2000.0, 10);
        Produto produto2 = new Produto("321", "Picanha", 10d, 10);
        Produto produto3 = new Produto("879", "Teclado Razer", 1000.0, 0);
        Produto produto4 = new Produto("012", "Samsung galaxy S7 64Gb", 3250.5, 0);
        Produto produto5 = new Produto("023", "Samsung galaxy S6 64Gb", 1d, 0);

        NavigableSet<Produto> produtoNavigableSet = new TreeSet<>();//NavigableSet é uma interface a classe que implementa essa interface uma delas é TreeSet
        produtoNavigableSet.add(produto1);
        produtoNavigableSet.add(produto2);
        produtoNavigableSet.add(produto3);
        produtoNavigableSet.add(produto4);
        produtoNavigableSet.add(produto5);

        for (Produto produto : produtoNavigableSet.descendingSet()) {
            System.out.println(produto);
        }
        System.out.println("--------------------");

        //Métodos para comparação:
        // lower <
        // floor <=
        // higher >
        // ceiling >=

        System.out.println(produtoNavigableSet.size());
        System.out.println(produtoNavigableSet.pollLast());
        System.out.println(produtoNavigableSet.size());
    }
}

```

```
Produto{serialNumero='012', nome='Samsung galaxy S7 64Gb', preco=3250.5, quantidade=0}
Produto{serialNumero='123', nome='Laptop Lenovo', preco=2000.0, quantidade=10}
Produto{serialNumero='879', nome='Teclado Razer', preco=1000.0, quantidade=0}
Produto{serialNumero='321', nome='Picanha', preco=10.0, quantidade=10}
Produto{serialNumero='023', nome='Samsung galaxy S6 64Gb', preco=1.0, quantidade=0}
--------------------
5
Produto{serialNumero='012', nome='Samsung galaxy S7 64Gb', preco=3250.5, quantidade=0}
4
```

## Coleções pt 18 - Map, HashMap, LinkedHashMap pt 01

`map` - tecnicamente o map não é uma collection;

- Para criar precisa passar qual o tipo da chave e valor;

- Primeira regra do map é que não pode ter chaves duplicadas;

- Para adicionar é o `put` e nao add;

```java
public class MapTest {
    public static void main(String[] args) {

        Map<String, String> map = new LinkedHashMap<>();//linked - mantem a ordem de inserção

        map.put("teklado", "teclado");
        map.put("mouze", "mouse");
        map.put("vc", "você");
        map.put("Meza", "mesa");
//        for(String key : map.values()){
//            System.out.println(key);
//        }
        for (Map.Entry<String, String> entry : map.entrySet()) {//entry tem acesso no mesmo for os dois atributos
            System.out.println(entry.getKey() + " " + entry.getValue());
        }
    }
}

```

```
teklado teclado
mouze mouse
vc você
Meza mesa

```

## Coleções pt 19 - Map, HashMap, LinkedHashMap pt 02

**`id auto gerado - usa a classe ThreadLocalRandom, no construtor this.id = ThreadLocalRandom.current().nextLong(0, 50)`**

```java
public class ConsumidorMapTest {
    public static void main(String[] args) {

        Consumidor consumidor = new Consumidor("William Suane", "122");
        Consumidor consumidor2 = new Consumidor("DevDojo", "321");
        Consumidor consumidor3 = new Consumidor("DevDojo", "321");


        Produto produto1 = new Produto("123", "Laptop Lenovo", 2000.0);
        Produto produto2 = new Produto("321", "Picanha", 26.4);
        Produto produto3 = new Produto("879", "Teclado Razer", 1000.0);
        Produto produto4 = new Produto("012", "Samsung galaxy S7 64Gb", 3250.5);

        Map<Consumidor, List<Produto>> map = new HashMap<>();

        List<Produto> produtosCon1 = new ArrayList<>();
        produtosCon1.add(produto1);
        produtosCon1.add(produto2);

        List<Produto> produtosCon2 = new ArrayList<>();
        produtosCon2.add(produto3);
        produtosCon2.add(produto4);

        map.put(consumidor, produtosCon1);
        map.put(consumidor2, produtosCon2);
        map.put(consumidor3, produtosCon1);

        for (Map.Entry<Consumidor, List<Produto>> entry : map.entrySet()) {
            System.out.println(entry.getKey().getNome());
            for (Produto produto : entry.getValue()) {
                System.out.println(produto.getNome());
            }
        }
    }
}

```

```
William Suane
Laptop Lenovo
Picanha
DevDojo
Laptop Lenovo
Picanha
```

## Coleções pt 21 - NavigableMap, TreeMap

```java
public class TreeMapTest {
    public static void main(String[] args) {

        NavigableMap<String, String> map = new TreeMap<>();
        map.put("A", "Letra A");
        map.put("D", "Letra D");
        map.put("B", "Letra B");
        map.put("C", "Letra C");
        for(Map.Entry<String, String> entry : map.entrySet()){
            System.out.println(entry.getKey() + " "+ entry.getValue());
        }
        System.out.println(map.headMap("C",true));
        System.out.println(map.higherEntry("C"));
        System.out.println(map.descendingMap());
    }
}

```

```
A Letra A
B Letra B
C Letra C
D Letra D
{A=Letra A, B=Letra B, C=Letra C}
D=Letra D
{D=Letra D, C=Letra C, B=Letra B, A=Letra A}
```
## Coleções pt 22 - Queue, PriorityQueue

`queue` fila (estrutura FIFO - First In, First Out) - tem por padrão a caracteristica primeiro entrar primeiro a sair;

```java
public class QueueTest {
    public static void main(String[] args) {

        Queue<String> queue = new PriorityQueue<>();
        queue.add("D");
        queue.add("A");
        queue.add("B");
        System.out.println(queue.remove("B"));
        System.out.println(queue.size());
    }
}

```

```
true
2
```