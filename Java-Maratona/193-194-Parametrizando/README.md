## Parametrizando comportamentos pt 01

**`EXEMPLO CHATGPT`**

**Parametrizar comportamentos** significa permitir que uma função ou método altere sua execução com base em parâmetros fornecidos. Isso é útil para criar código mais flexível e reutilizável.

Aqui está um exemplo simples em Java, implementando um comportamento parametrizado para calcular descontos.

**Exemplo: Cálculo de Descontos**

Neste exemplo, temos diferentes estratégias de desconto que podem ser parametrizadas em um método.

```java
public class Parametrizacao {

    // Método principal
    public static void main(String[] args) {
        // Produto com preço base
        double precoProduto = 100.0;

        // Aplicando diferentes descontos
        System.out.println("Desconto de Natal: " + calcularDesconto(precoProduto, "natal"));
        System.out.println("Desconto de Ano Novo: " + calcularDesconto(precoProduto, "anonovo"));
        System.out.println("Sem desconto: " + calcularDesconto(precoProduto, "nenhum"));
    }

    // Método para calcular desconto baseado no tipo
    public static double calcularDesconto(double preco, String tipoDesconto) {
        switch (tipoDesconto.toLowerCase()) {
            case "natal":
                return preco * 0.9; // 10% de desconto
            case "anonovo":
                return preco * 0.85; // 15% de desconto
            case "nenhum":
            default:
                return preco; // Sem desconto
        }
    }
}
```

`Predicate` é uma interface você precisa passar uma subclasse, uma classe concreta que tem o comportamento do teste;

**`EXEMPLO CHATGPR PREDICATE`**

Usada para representar uma função que recebe um argumento e retorna um booleano.

Exemplo: Filtrando números com Predicate

Aqui, usaremos Predicate para verificar se um número atende a uma determinada condição.

```java
public class ExemploPredicate {

    public static void main(String[] args) {
        int numero = 10;

        // Verificar se o número é par
        Predicate<Integer> ehPar = n -> n % 2 == 0;
        System.out.println("O número é par? " + ehPar.test(numero));

        // Verificar se o número é maior que 5
        Predicate<Integer> maiorQueCinco = n -> n > 5;
        System.out.println("O número é maior que 5? " + maiorQueCinco.test(numero));

        // Combinar os dois Predicates: número par e maior que 5
        Predicate<Integer> parEMaiorQueCinco = ehPar.and(maiorQueCinco);
        System.out.println("O número é par e maior que 5? " + parEMaiorQueCinco.test(numero));
    }
}
```
