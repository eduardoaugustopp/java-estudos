## Coleções pt 06 - List pt 01

`List` - uma coleção ordenada ou seja que tem uma sequência;

As listas no pacote `Collection` ele é altamente coeso, geralmente trabalha orientado a interface e você precisa de um tipo para esse objeto; tem vários tipos que pode criar mas o mais simples é o `ArrayList` e esse `ArrayList` ele é basicamente um Array que pode ser dinamicamente incrementado; era feito até a versão 1.4;

Precisa definir o tipo na variavel de referencia;

```java
public static void main(String[] args) {
		List<String> nomes = new ArrayList<>(16);

        // Adicionando elementos à lista
        nomes.add("William");
        nomes.add("DevDojo Academy");

        // Iterando sobre a lista usando um for-each
        for (String nome : nomes) {
            System.out.println(nome);
        }

        System.out.println(" -------- ");

        // Adicionando mais um elemento
        nomes.add("Suane");

        // Iterando sobre a lista usando um for com índice
        for (int i = 0; i < nomes.size(); i++) {
            System.out.println(nomes.get(i));
        }

	}
```

```
William
DevDojo Academy
 --------
William
DevDojo Academy
Suane
```

## Coleções pt 07 - List pt 02

Tranformar 2 Arrays em 1 Array só `addAll`;

```java
        List<String> nomes = new ArrayList<>(16);
		List<String> nomes2 = new ArrayList<>(16);

        // Adicionando elementos à lista
        nomes.add("William");
        nomes.add("DevDojo Academy");

        nomes2.add("Teste");

        nomes.addAll(nomes2);

        // Iterando sobre a lista usando um for-each
        for (String nome : nomes) {
            System.out.println(nome);
        }

        System.out.println(" -------- ");

        // Adicionando mais um elemento
        nomes.add("Suane");

        // Iterando sobre a lista usando um for com índice
        for (int i = 0; i < nomes.size(); i++) {
            System.out.println(nomes.get(i));
        }
```

```
William
DevDojo Academy
Teste
 --------
William
DevDojo Academy
Teste
Suane
```

## Coleções pt 08 - List pt 03

`Usando objeto nas listas, Exemplo.:`

Imagina que esses objetos estão salvos no banco de dados, esses dados vão virar objetos na nossa aplicação java você quer gerenciar esses objetos porque não vai trabalhar com objetos individuais, geralmente vai trabalhar com uma coleção de objetos ai pode criar uma lista para guardar esses Smartphone;

`contains` e `indexOf` usam o método `equals`;

- contains(s4): Verifica se existe algum objeto na lista que é igual a s4 → Retorna true.
- indexOf(s4): Retorna a posição (índice) do primeiro objeto igual a s4 → Retorna 1.

```java
public class SmartphoneListTest01 {
    public static void main(String[] args) {
        Smartphone s1 = new Smartphone("1ABC1", "iPhone");
        Smartphone s2 = new Smartphone("22222", "Pixel");
        Smartphone s3 = new Smartphone("33333", "Samsung");

        List<Smartphone> smartphones = new ArrayList<>(6);
        smartphones.add(s1);
        smartphones.add(s2);
        smartphones.add(s3);

        for (Smartphone smartphone : smartphones) {
            System.out.println(smartphone);
        }

        Smartphone s4 = new Smartphone("22222", "Pixel");
        System.out.println(smartphones.contains(s4));

        int indexSmartphone4 = smartphones.indexOf(s4);
        System.out.println(indexSmartphone4);
    }
}
```
