## Padrões de Projeto pt 01 - Builder

`design patterns` `padrões de projeto` - é uma forma de escrever código que vai resolver um problema comum;

`builder` - uma forma que você tem de organizar a criação desse objeto de uma forma que qualquer um consiga entender, consiga ver de forma fácil;

```java
public class Person {
    private String firstName;
    private String lastName;
    private String username;
    private String email;

    private Person(String firstName, String lastName, String username, String email) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.username = username;
        this.email = email;
    }

    @Override
    public String toString() {
        return "Person{" +
                "firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", username='" + username + '\'' +
                ", email='" + email + '\'' +
                '}';
    }


    public static final class PersonBuilder {
        private String firstName;
        private String lastName;
        private String username;
        private String email;

        public PersonBuilder() {
        }

        public PersonBuilder firstName(String firstName) {
            this.firstName = firstName;
            return this;
        }

        public PersonBuilder lastName(String lastName) {
            this.lastName = lastName;
            return this;
        }

        public PersonBuilder username(String username) {
            this.username = username;
            return this;
        }

        public PersonBuilder email(String email) {
            this.email = email;
            return this;
        }

        public Person build() {
            return new Person(firstName, lastName, username, email);
        }
    }
}
```

```java
public class BuilderPatternTest01 {
    public static void main(String[] args) {
        Person build = new Person.PersonBuilder()
                .firstName("William")
                .lastName("Suane")
                .username("ViradoNoJiraya")
                .email("william.suane@devdojo.academy")
                .build();
        System.out.println(build);

    }
}
```

## Padrões de Projeto pt 02 - Factory

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public enum Country {
    BRAZIL, USA
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public interface Currency {
    String getSymbol();
}

class Real implements Currency {
    @Override
    public String getSymbol() {
        return "R$";
    }
}

class UsDollar implements Currency {
    @Override
    public String getSymbol() {
        return "$";
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public class CurrencyFactory {
    public static Currency newCurrency(Country country) {
        switch (country) {
            case USA:
                return new UsDollar();
            case BRAZIL:
                return new Real();
            default: throw new IllegalArgumentException("No currency found for this country");
        }
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public class Person {
    private String firstName;
    private String lastName;
    private String username;
    private String email;

    private Person(String firstName, String lastName, String username, String email) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.username = username;
        this.email = email;
    }

    @Override
    public String toString() {
        return "Person{" +
                "firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", username='" + username + '\'' +
                ", email='" + email + '\'' +
                '}';
    }


    public static final class PersonBuilder {
        private String firstName;
        private String lastName;
        private String username;
        private String email;

        private PersonBuilder() {
        }

        public static PersonBuilder builder() {
            return new PersonBuilder();
        }

        public PersonBuilder firstName(String firstName) {
            this.firstName = firstName;
            return this;
        }

        public PersonBuilder lastName(String lastName) {
            this.lastName = lastName;
            return this;
        }

        public PersonBuilder username(String username) {
            this.username = username;
            return this;
        }

        public PersonBuilder email(String email) {
            this.email = email;
            return this;
        }

        public Person build() {
            return new Person(firstName, lastName, username, email);
        }
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.test;

import academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio.Country;
import academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio.Currency;
import academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio.CurrencyFactory;

public class CurrencyFactoryTest01 {
    public static void main(String[] args) {
        Currency currency = CurrencyFactory.newCurrency(Country.BRAZIL);
        System.out.println(currency.getSymbol());
    }
}
```

## Padrões de Projeto pt 03 - Singleton pt 01 - Eager Initialization

`Singleton` - em determinado momento você vai precisar criar uma classe aonde ela só pode ter um objeto e esse objeto tem que ser compartilhado com qualquer pessoa, qualquer threads, qualquer parte do seu código;

- Conexão com banco de dados é um claro exemplo de singleton;

**`EXEMPLO CHATGPT`**

O padrão de projeto Singleton é utilizado para garantir que uma classe tenha apenas uma única instância durante o tempo de execução de um programa. Ele também fornece um ponto global de acesso a essa instância.

`Eager Initialization`

No padrão Singleton com "Eager Initialization", a instância é criada no momento em que a classe é carregada na memória. Isso significa que a instância está disponível imediatamente, independentemente de ela ser necessária ou não. Esse método é simples de implementar, mas pode ser ineficiente se a instância não for utilizada.

Vamos imaginar que você tem uma fábrica de carros e deseja garantir que existe apenas uma única fábrica durante toda a execução do programa. A fábrica será responsável por criar carros.

Implementação do Singleton para a Fábrica de Carros

```java
public class CarFactory {

    // Instância única da Fábrica
    private static CarFactory instance;

    // Construtor privado para evitar instâncias externas
    private CarFactory() {
        System.out.println("Fábrica de Carros criada!");
    }

    // Método público para acessar a instância única
    public static CarFactory getInstance() {
        if (instance == null) {
            instance = new CarFactory();
        }
        return instance;
    }

    // Método para criar um carro
    public void createCar(String model) {
        System.out.println("Carro modelo " + model + " criado!");
    }
}
```

Como usar a Fábrica de Carros Singleton

```java
public class Main {
    public static void main(String[] args) {
        // Obtendo a instância única da fábrica
        CarFactory factory = CarFactory.getInstance();

        // Criando carros através da fábrica
        factory.createCar("Ferrari");
        factory.createCar("Lamborghini");

        // Tentando obter outra instância da fábrica (deve ser a mesma)
        CarFactory anotherFactory = CarFactory.getInstance();
        System.out.println("As instâncias são iguais? " + (factory == anotherFactory));
    }
}
```

```
Fábrica de Carros criada!
Carro modelo Ferrari criado!
Carro modelo Lamborghini criado!
As instâncias são iguais? true
```

- Construtor privado: Garante que ninguém possa criar uma instância de CarFactory diretamente com new.
- Instância única: A variável instance guarda a única instância da fábrica.
- Controle de acesso: O método getInstance é usado para obter a instância única.
- Função prática: A fábrica cria carros através do método createCar.

## Padrões de Projeto pt 04 - Singleton pt 02 - Lazy Initialization

**`EXEMPLO CHATGPT`**

O padrão de projeto Singleton garante que uma classe tenha apenas uma única instância em todo o ciclo de vida de uma aplicação e fornece um ponto global de acesso a essa instância. O conceito de Lazy Initialization no Singleton se refere a atrasar a criação da instância até que ela seja realmente necessária, diferentemente da inicialização eager, onde a instância é criada antecipadamente.

Imaginando que temos uma classe FabricaVeiculos que gerencia a criação de veículos e só pode existir uma instância dessa fábrica no sistema.

A classe FabricaVeiculos é a classe Singleton que controla a criação de veículos. Só deve existir uma instância dessa classe.

```java
public class FabricaVeiculos {
    // Instância única da fábrica, inicializada como null
    private static FabricaVeiculos instance;

    // Construtor privado
    private FabricaVeiculos() {}

    // Método para acessar a instância única da fábrica
    public static FabricaVeiculos getInstance() {
        if (instance == null) {
            instance = new FabricaVeiculos(); // Instância é criada pela primeira vez
        }
        return instance;
    }

    // Método para criar um novo veículo
    public Veiculo criarVeiculo(String modelo, String marca) {
        return new Veiculo(modelo, marca);
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        // Obter a instância da fábrica de veículos (Singleton)
        FabricaVeiculos fabrica = FabricaVeiculos.getInstance();

        // Criar alguns veículos
        Veiculo veiculo1 = fabrica.criarVeiculo("Fusca", "Volkswagen");
        Veiculo veiculo2 = fabrica.criarVeiculo("Civic", "Honda");

        // Exibir os veículos criados
        System.out.println(veiculo1);
        System.out.println(veiculo2);

        // Teste de instância única
        FabricaVeiculos fabrica2 = FabricaVeiculos.getInstance();
        System.out.println(fabrica == fabrica2); // True, ambas são a mesma instância
    }
}
```

O Lazy Initialization entra em cena no momento em que a instância da classe FabricaVeiculos é criada. No exemplo fornecido, a instância da fábrica só é criada quando o método getInstance() é chamado pela primeira vez, em vez de ser criada no momento da inicialização da classe.

O que é Lazy Initialization aqui?

A Lazy Initialization garante que a instância da fábrica não será criada até que seja realmente necessária. Em outras palavras, o primeiro acesso à fábrica de veículos irá instanciá-la. Isso evita a criação de uma instância desnecessária caso a fábrica nunca seja usada.

## Padrões de Projeto pt 05 - Singleton pt 03 - Singleton with enumeration

**`EXEMPLO CHATGPT`**

O padrão Singleton pode ser implementado de uma forma mais segura e eficiente usando enumerações (enums) em Java. Essa abordagem é recomendada para garantir que o Singleton seja de forma segura, eficiente e à prova de falhas em ambientes multithread.

Singleton com Enumeração

A principal vantagem de usar um enum para implementar o Singleton é que a linguagem Java garante, por padrão, que qualquer valor de enum seja instanciado apenas uma vez. Isso torna o padrão Singleton mais simples, seguro e com menos risco de falhas.

Implementação com Enum

Aqui está como você pode implementar o padrão Singleton utilizando uma enumeração:

```java
public enum FabricaVeiculos {
    // A única instância da fábrica
    INSTANCE;

    // Método para criar um novo veículo
    public Veiculo criarVeiculo(String modelo, String marca) {
        return new Veiculo(modelo, marca);
    }
}
```

Como funciona?

- A constante INSTANCE representa a única instância da classe FabricaVeiculos. No entanto, o Java garante que a instância seja criada de forma única e thread-safe, sem a necessidade de sincronização manual.
- Como INSTANCE é uma enumeração, a criação da instância é tratada pela JVM de forma segura e eficiente, sem a necessidade de verificações ou bloqueios explícitos.

```java
public class Veiculo {
    private String modelo;
    private String marca;

    public Veiculo(String modelo, String marca) {
        this.modelo = modelo;
        this.marca = marca;
    }

    public String getModelo() {
        return modelo;
    }

    public String getMarca() {
        return marca;
    }

    @Override
    public String toString() {
        return "Veículo [Modelo=" + modelo + ", Marca=" + marca + "]";
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        // Obtém a instância da fábrica utilizando o Singleton com Enum
        FabricaVeiculos fabrica = FabricaVeiculos.INSTANCE;

        // Cria alguns veículos
        Veiculo veiculo1 = fabrica.criarVeiculo("Fusca", "Volkswagen");
        Veiculo veiculo2 = fabrica.criarVeiculo("Civic", "Honda");

        // Exibe os veículos criados
        System.out.println(veiculo1);
        System.out.println(veiculo2);

        // Verifica se a instância é única
        FabricaVeiculos fabrica2 = FabricaVeiculos.INSTANCE;
        System.out.println(fabrica == fabrica2); // true, ambas são a mesma instância
    }
}

```

## Padrões de Projeto pt 06 - Data Transfer Object

`DTO` - se precisar fazer uma transferência desses objetos diretamente para uma outra API digamos cada um desses atributos, pega e monta uma só, fazendo um agrupamento dos atributos que você precisa;

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

import java.util.HashSet;
import java.util.Set;

public final class Aircraft {
    private final Set<String> availableSeats = new HashSet<>();
    private final String name;

    public Aircraft(String name) {
        this.name = name;
    }

    {
        availableSeats.add("1A");
        availableSeats.add("1B");
    }

    public boolean bookSeat(String seat) {
        return availableSeats.remove(seat);
    }

    public String getName() {
        return name;
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

import java.util.HashSet;
import java.util.Set;

public final class AircraftSingletonEager {
    //Eager initialization
    private static final AircraftSingletonEager INSTANCE = new AircraftSingletonEager("787-900");
    private final Set<String> availableSeats = new HashSet<>();
    private final String name;

    private AircraftSingletonEager(String name) {
        this.name = name;
    }

    {
        availableSeats.add("1A");
        availableSeats.add("1B");
    }

    public static AircraftSingletonEager getINSTANCE() {
        return INSTANCE;
    }

    public boolean bookSeat(String seat) {
        return availableSeats.remove(seat);
    }

}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

import java.util.HashSet;
import java.util.Set;

public enum AircraftSingletonEnum {
    INSTANCE;
    private final Set<String> availableSeats;

    AircraftSingletonEnum() {
        this.availableSeats = new HashSet<>();
        this.availableSeats.add("1A");
        this.availableSeats.add("1B");
    }

    public boolean bookSeat(String seat) {
        return availableSeats.remove(seat);
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

import java.util.HashSet;
import java.util.Set;

public final class AircraftSingletonLazy {

    private static AircraftSingletonLazy INSTANCE;
    private final Set<String> availableSeats = new HashSet<>();
    private final String name;

    private AircraftSingletonLazy(String name) {
        this.name = name;
    }

    {
        availableSeats.add("1A");
        availableSeats.add("1B");
    }

    public static AircraftSingletonLazy getINSTANCE() {
        if (INSTANCE == null) {
            synchronized (AircraftSingletonLazy.class) {
                if (INSTANCE == null) {
                    INSTANCE = new AircraftSingletonLazy("787-900");
                }
            }
        }
        return INSTANCE;
    }

    public boolean bookSeat(String seat) {
        return availableSeats.remove(seat);
    }

}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public enum Country {
    BRAZIL, USA
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public interface Currency {
    String getSymbol();
}

class Real implements Currency {
    @Override
    public String getSymbol() {
        return "R$";
    }
}

class UsDollar implements Currency {
    @Override
    public String getSymbol() {
        return "$";
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public class CurrencyFactory {
    public static Currency newCurrency(Country country) {
        switch (country) {
            case USA:
                return new UsDollar();
            case BRAZIL:
                return new Real();
            default: throw new IllegalArgumentException("No currency found for this country");
        }
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public class Person {
    private String firstName;
    private String lastName;
    private String username;
    private String email;

    private Person(String firstName, String lastName, String username, String email) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.username = username;
        this.email = email;
    }

    @Override
    public String toString() {
        return "Person{" +
                "firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", username='" + username + '\'' +
                ", email='" + email + '\'' +
                '}';
    }


    public static final class PersonBuilder {
        private String firstName;
        private String lastName;
        private String username;
        private String email;

        private PersonBuilder() {
        }

        public static PersonBuilder builder() {
            return new PersonBuilder();
        }

        public PersonBuilder firstName(String firstName) {
            this.firstName = firstName;
            return this;
        }

        public PersonBuilder lastName(String lastName) {
            this.lastName = lastName;
            return this;
        }

        public PersonBuilder username(String username) {
            this.username = username;
            return this;
        }

        public PersonBuilder email(String email) {
            this.email = email;
            return this;
        }

        public Person build() {
            return new Person(firstName, lastName, username, email);
        }
    }

    public String getFirstName() {
        return firstName;
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio;

public class ReportDto {
    private String aircraftName;
    private Country country;
    private Currency currency;
    private String personName;

    public static final class ReportDtoBuilder {
        private String aircraftName;
        private Country country;
        private Currency currency;
        private String personName;

        private ReportDtoBuilder() {
        }

        public static ReportDtoBuilder builder() {
            return new ReportDtoBuilder();
        }

        public ReportDtoBuilder aircraftName(String aircraftName) {
            this.aircraftName = aircraftName;
            return this;
        }

        public ReportDtoBuilder country(Country country) {
            this.country = country;
            return this;
        }

        public ReportDtoBuilder currency(Currency currency) {
            this.currency = currency;
            return this;
        }

        public ReportDtoBuilder personName(String personName) {
            this.personName = personName;
            return this;
        }

        public ReportDto build() {
            ReportDto reportDto = new ReportDto();
            reportDto.currency = this.currency;
            reportDto.aircraftName = this.aircraftName;
            reportDto.personName = this.personName;
            reportDto.country = this.country;
            return reportDto;
        }
    }

    @Override
    public String toString() {
        return "ReportDto{" +
                "aircraftName='" + aircraftName + '\'' +
                ", country=" + country +
                ", currency=" + currency +
                ", personName='" + personName + '\'' +
                '}';
    }
}
```

```java
package academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.test;

import academy.devdojo.maratonajava.javacore.ZZHpadroesdeprojeto.dominio.*;

public class DataTransferObjectTest01 {
    public static void main(String[] args) {
        Aircraft aircraft = new Aircraft("777");
        Country country = Country.BRAZIL;
        Currency currency = CurrencyFactory.newCurrency(country);
        Person person = Person.PersonBuilder
                .builder()
                .firstName("William")
                .lastName("Suane")
                .build();
        ReportDto reportDto = ReportDto.ReportDtoBuilder.builder()
                .aircraftName(aircraft.getName())
                .country(country)
                .currency(currency)
                .personName(person.getFirstName())
                .build();

        System.out.println(reportDto);
    }
}
```
