## Classes Internas pt 01 - Introdução

`Classes Internas` - basicamente é uma classe dentro da outra;

- Para criar um objeto da classe interna, você precisa de um objeto da classe externa ele está fortemente linkado com a classe externa;

```java
public class ClassesInternasTest {
    private String nome = "Eduardo";
    public class Interna{
        public void verClassExterna(){
            System.out.println(nome);
            System.out.println(this);
            System.out.println(ClassesInternasTest.this);
        }
    }

    public static void main(String[] args) {

        ClassesInternasTest externa = new ClassesInternasTest();

        ClassesInternasTest.Interna in = externa.new Interna();

        ClassesInternasTest.Interna in2 = new ClassesInternasTest(). new Interna();

        in.verClassExterna();
    }
}

/*class Externa{
    private String nome = "Eduardo";

    class Interna{
        public void verClasseExterna){
            System.out.println(nome);
        }
    }
}*/

```

## Classes Internas pt 02 - Classes Locais

**Não é utilizada com frequencia**

**`EXEMPLO CHATGPT`**

1. Definição da Classe Local:

A classe Processador é definida dentro do método executarProcesso.

Só pode ser acessada dentro do método onde foi definida.

2. Uso:

A classe local é usada para encapsular lógica que só faz sentido dentro do método onde foi criada.

No exemplo, ela processa uma mensagem fornecida.

```java
public class ExemploClasseLocal {

    public void executarProcesso() {
        // Classe local dentro do método executarProcesso
        class Processador {
            private String mensagem;

            public Processador(String mensagem) {
                this.mensagem = mensagem;
            }

            public void processar() {
                System.out.println("Processando: " + mensagem);
            }
        }

        // Instanciando a classe local
        Processador processador = new Processador("Classe Local em Ação!");
        processador.processar();
    }

    public static void main(String[] args) {
        ExemploClasseLocal exemplo = new ExemploClasseLocal();
        exemplo.executarProcesso();
    }
}
```

## Classes Internas pt 03 - Classes Anônimas

`Classes Anônimas` são classes que vão existir brevemente por um determinado período de tempo no código e que não pode ser reutilizada em nenhum outro lugar;

```java
class Animal{
    public void andar(){
        System.out.println("Andando...");
    }
}

public class ClassesAnonimasTest {
    public static void main(String[] args) {
        Animal a = new Animal(){
            @Override
            public void andar() {
                System.out.println("Andando Anonimamente!!!");
            }

            public void correr(){ /* Não é possível!!*/
                System.out.println("CORRENDO!");
            }
        };
        a.andar();

        List<Carro> carroList  = new ArrayList<>();
        carroList.add(new Carro("FERRARI"));
        carroList.add(new Carro("AUDI"));
        carroList.add(new Carro("BMW"));
        Collections.sort(carroList, new Comparator<Carro>(){
            @Override
            public int compare(Carro o1, Carro o2) {
                return o1.getNome().compareTo(o2.getNome());
            }
        });
        System.out.println(carroList);

    }
}

```

```
Andando Anonimamente!!!
[Carro{nome='AUDI'}, Carro{nome='BMW'}, Carro{nome='FERRARI'}]
```

## Classes Internas pt 04 - Classes aninhadas estáticas

- As classes aninhadas estáticas (ou static nested classes) são definidas dentro de outra classe com o modificador static. Elas são associadas à classe externa e não a uma instância dela, o que significa que podem ser usadas sem criar um objeto da classe externa.

**Características de Classes Aninhadas Estáticas:**

1. Independência da Classe Externa:

Não podem acessar diretamente membros de instância da classe externa; apenas membros estáticos.

2. Usos Comuns:

São usadas quando a classe interna não precisa de uma referência à instância da classe externa.

3. Declaração:

Declaradas com o modificador static.

**`EXEMPLO CHATGPT`**

```java
public class ClasseExterna {

    // Atributo estático
    private static String mensagem = "Olá, Classe Aninhada Estática!";

    // Classe aninhada estática
    public static class ClasseAninhada {
        public void exibirMensagem() {
            // Pode acessar membros estáticos da classe externa
            System.out.println(mensagem);
        }
    }

    public static void main(String[] args) {
        // Instanciando a classe aninhada estática
        ClasseExterna.ClasseAninhada aninhada = new ClasseExterna.ClasseAninhada();
        aninhada.exibirMensagem();
    }
}
```
