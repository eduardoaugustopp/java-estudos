## Generics pt 01 - Introdução

```java
public class GenericsTest {
    public static void main(String[] args) {
        // type erasure
        List<String> lista = new ArrayList<>();
        lista.add("String");
        lista.add("String");
        lista.add("Eduardo");

        for(String obj : lista){
            System.out.println(obj);
        }
        add(lista, 1L);//O método add() recebe uma lista e um valor do tipo Long. Aqui, o valor 1L (um Long) é adicionado à lista lista, que foi declarada para aceitar apenas String. Isso vai gerar um erro em tempo de execução.


        /*for(String obj : lista){
            System.out.println(obj);
        }*/
        /*
        Exception in thread "main" java.lang.ClassCastException: java.lang.Long cannot be cast to java.lang.String
	    at br.com.abc.javacore.generics.testes.GenericsTest.main(GenericsTest.java:18)
	    */
    }

    public static void add(List lista, Long l){
        lista.add(l);// O método add não utiliza Generics corretamente. Ele recebe uma List sem tipo (sem generics), o que significa que ele pode aceitar qualquer tipo de objeto, e tenta adicionar um valor Long (1L) à lista.
    }
}

```

**`EXEMPLO CHATGPT SOLUÇÃO`**

- Para resolver o problema, você deve garantir que o método add seja genérico e compatível com o tipo da lista. Modificando o código para usar Generics corretamente, você evita o erro de tipo:

```java
public class GenericsTest {
    public static void main(String[] args) {
        List<String> lista = new ArrayList<>();
        lista.add("String");
        lista.add("String");
        lista.add("Eduardo");

        for(String obj : lista) {
            System.out.println(obj);
        }

        // Remover a chamada com tipo incorreto
        // add(lista, 1L); // Isso geraria erro de compilação

        // Certifique-se de adicionar somente Strings na lista
    }

    // Usar Generics corretamente
    public static <T> void add(List<T> lista, T item) {
        lista.add(item);
    }
}
```

- Agora, o método add é genérico (<T>), o que permite que você adicione qualquer tipo de objeto à lista, mas garante que o tipo da lista será preservado, evitando problemas de tipo.

- T é usado como um tipo genérico em Java para indicar que o tipo de um parâmetro ou variável será determinado em tempo de compilação, quando o código for utilizado.

- O T pode ser substituído por qualquer nome, mas por convenção, a letra T é a mais comum, especialmente para tipos. Outros exemplos de letras incluem E (para elementos), K (para chaves em mapas), V (para valores em mapas), e assim por diante.

## Generics pt 02 - Wildcard pt 01

`Wildcard` - caractere coringa;

```java
abstract class Animal{
    public abstract void consulta();
}

class Cachorro extends Animal implements Comparable{
    @Override
    public void consulta() {
        System.out.println("Consultando Cachorro");
    }

    @Override
    public int compareTo(Object o) {
        return 0;
    }
}

class Gato extends Animal{
    @Override
    public void consulta() {
        System.out.println("Consultando GATO!");
    }
}

public class WildCardTest {
    public static void main(String[] args) {
        Cachorro[] cachorros = {new Cachorro(), new Cachorro()};
        Gato[] gatos = {new Gato(), new Gato()};

        //consultarAnimais(cachorros);
        //consultarAnimais(gatos);

        List<Cachorro> cachorroList = new ArrayList<>();
        cachorroList.add(new Cachorro());

        List<Gato> gatoList = new ArrayList<>();
        gatoList.add(new Gato());

        consultaAnimalList(cachorroList);
        consultaAnimalList(gatoList);
        ordernaLista(cachorroList);

    }

    public static void consultarAnimais(Animal[] animals){
        for(Animal animal : animals){
            animal.consulta();
        }
    }

    // type erasure | ?
    public static void consultaAnimalList(List<? extends Animal> animals){
        for(Animal animal : animals){
            animal.consulta();
        }
    }
    public static void consultaCachorroList(List<? super Cachorro> cachorroList){
        Cachorro c1 = new Cachorro();
        Animal c2 = new Cachorro();
        Object o = new Cachorro();
        cachorroList.add(new Cachorro());
    }

    public static void ordernaLista(List<? extends Comparable> lista){
        Collections.sort(lista);
    }

}

```

## Generics pt 04 - Classes Genéricas pt 01

Criando nossas classes genéricas:

**Não generica**

```java
class CarroAlugavel{
    private List<Carro> carroDisponiveis = new ArrayList<>();

    {
        carroDisponiveis.add(new Carro("GOL"));
        carroDisponiveis.add(new Carro("BMW"));
    }

    //alugar
    public Carro getCarroDisponivel(){
        Carro c = carroDisponiveis.remove(0);
        System.out.println("Alugando Carro " + c);
        System.out.println("Corros disponíveis "+ carroDisponiveis);
        return c;
    }
    public void devolverCarro(Carro c){
        System.out.println("DEVOLVENDO carro "+ c);
        carroDisponiveis.add(c);
        System.out.println("Corros disponíveis "+ carroDisponiveis);
    }
}

class ComputadorAlugavel {
    private List<Computador> computadoresDisponiveis = new ArrayList<>();
    {
        computadoresDisponiveis.add(new Computador("Alienware"));
        computadoresDisponiveis.add(new Computador("HP"));
    }
    //Alugar
    public Computador getComputadorDisponivel(){
        Computador c = computadoresDisponiveis.remove(0);
        System.out.println("Alugando pc: "+c);
        System.out.println("PC disponiveis: "+ computadoresDisponiveis);
        return c;
    }
    public void devolverComputador(Computador c){
        System.out.println("Devolvendo PC: "+c);
        computadoresDisponiveis.add(c);
        System.out.println("PC disponiveis: "+ computadoresDisponiveis);
    }
}

public class ClasseNaoGenericaTeste {
    public static void main(String[] args) {

        CarroAlugavel carroAlugavel = new CarroAlugavel();
        Carro carroAlugado = carroAlugavel.getCarroDisponivel();
        System.out.println("USANDO O CARRO POR UM MES");
        carroAlugavel.devolverCarro(carroAlugado);

        System.out.println(" -----------------------  ");

        ComputadorAlugavel computadorAlugavel = new ComputadorAlugavel();
        Computador computador = computadorAlugavel.getComputadorDisponivel();
        System.out.println("Usando o PC");
        computadorAlugavel.devolverComputador(computador);
    }
}

```

**Generica**

```java
public class ClasseGenericaTeste {
    public static void main(String[] args) {
        List<Carro> carrosDisponiveis = new ArrayList<>();
        carrosDisponiveis.add(new Carro("Gol"));
        carrosDisponiveis.add(new Carro("BMW"));
        ObjetosAlugaveis<Carro> carroAlugavel = new ObjetosAlugaveis<>(carrosDisponiveis);
        Carro carro = carroAlugavel.getObjetoDisponivel();
        System.out.println("Usando o carro por um mes");
        carroAlugavel.devolverObjeto(carro);
        System.out.println("---------");

        List<Computador> computadoresDisponiveis = new ArrayList<>();
        computadoresDisponiveis.add(new Computador("Alienware"));
        computadoresDisponiveis.add(new Computador("HP"));
        ObjetosAlugaveis<Computador> computadorAlugavel = new ObjetosAlugaveis<>(computadoresDisponiveis);
        Computador c = computadorAlugavel.getObjetoDisponivel();
        System.out.println("Usando computador");
        computadorAlugavel.devolverObjeto(c);
    }
}

class ObjetosAlugaveis<T>{//não sei o que é a lista mas sei que a lista vai ser baseado na hora que criar o objeto e colocar o tipo por isso coloca a letra T
    private List<T> objetosDiponiveis;

    public ObjetosAlugaveis(List<T> objetosDiponiveis) {
        this.objetosDiponiveis = objetosDiponiveis;
    }
    //Alugar
    public T getObjetoDisponivel() {
        T t = objetosDiponiveis.remove(0);
        System.out.println("Alugando objeto: " + t);
        System.out.println("Objetos disponiveis: " + objetosDiponiveis);
        return t;
    }

    public void devolverObjeto(T t) {
        System.out.println("Devolvendo objeto: " + t);
        objetosDiponiveis.add(t);
        System.out.println("Objetos disponiveis: " + objetosDiponiveis);
    }
}

class DoisAtributos<T,X>{
    T um;
    X dois;

    public DoisAtributos(T um, X dois) {
        this.um = um;
        this.dois = dois;
    }

    public T getUm() {
        return um;
    }

    public void setUm(T um) {
        this.um = um;
    }

    public X getDois() {
        return dois;
    }

    public void setDois(X dois) {
        this.dois = dois;
    }
}
```

## Generics pt 06 - Métodos Genéricos

```java
public class ClassesLocaisTest {

    private String nome = " eduardo";

    public void fazAlgumaCoisa(){
        String sobrenome = "Eduardo"; // por padrão = final

        class Interna{ // abstract ou final
            public void imprimeNomeExterno(){
                System.out.println(nome);
                System.out.println(sobrenome);
            }
        }
        Interna in = new Interna();
        in.imprimeNomeExterno();
    }



    public static void main(String[] args) {

        ClassesLocaisTest externa = new ClassesLocaisTest();
        externa.fazAlgumaCoisa();
    }
}

```


