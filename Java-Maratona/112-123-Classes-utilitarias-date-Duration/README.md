## Classes Utilitárias - Date e Calendar

`date`- usado em sistemas legados, sistema moderno não usa;

`calendar` - é uma classe abstract não é possivel usar new;

```java
public class ManipulacaoDatas {

    public static void main(String[] args) {
        Date date = new Date();
        date.setTime(date.getTime() + 3_600_000L);
        System.out.println(date);

        Calendar c = Calendar.getInstance();
        c.setTime(date);
        System.out.println(c);

        if(Calendar.SUNDAY == c.getFirstDayOfWeek()){
            System.out.println("Domingo é o primeiro dia da semana");
        }

        System.out.println(Calendar.DAY_OF_MONTH);
        System.out.println(Calendar.DAY_OF_WEEK);
        System.out.println(Calendar.DAY_OF_YEAR);

        c.roll(Calendar.HOUR, 24); // so mexe no dado hora
        c.add(Calendar.HOUR, 24); // ajusta relogio geral

        Date date2 = c.getTime();
        System.out.println(date2);
    }
}

```

## Classes Utilitárias - DateFormat

```java
public class DateFormatTeste {
    public static void main(String[] args) {
        Calendar c = Calendar.getInstance();
        DateFormat[] dfa = new DateFormat[6];

        dfa[0] = DateFormat.getInstance();
        dfa[1] = DateFormat.getDateInstance();
        dfa[2] = DateFormat.getDateInstance(DateFormat.SHORT);
        dfa[3] = DateFormat.getDateInstance(DateFormat.MEDIUM);
        dfa[4] = DateFormat.getDateInstance(DateFormat.LONG);
        dfa[5] = DateFormat.getDateInstance(DateFormat.FULL);

        for(DateFormat df: dfa){
            System.out.println(df.format(c.getTime()));
        }
        /*
            23/12/2024 17:08
            23 de dez. de 2024
            23/12/2024
            23 de dez. de 2024
            23 de dezembro de 2024
            segunda-feira, 23 de dezembro de 2024
        */

    }
}

```

## Classes Utilitárias - Internacionalização Datas com Locale

```java
 public static void main(String[] args) {
        Locale locItaly = Locale.of("it", "IT"); // Italiano (Itália)
        Locale locSuica = Locale.of("it", "CH"); // Italiano (Suíça)
        Locale locIndia = Locale.of("hi", "IN"); // Hindi (Índia)
        Locale locJapao = Locale.of("ja");       // Japonês

        Calendar c = Calendar.getInstance();

        DateFormat df = DateFormat.getDateInstance(DateFormat.FULL, locItaly);
        DateFormat df2 = DateFormat.getDateInstance(DateFormat.FULL, locSuica);
        DateFormat df3 = DateFormat.getDateInstance(DateFormat.FULL, locIndia);
        DateFormat df4 = DateFormat.getDateInstance(DateFormat.FULL, locJapao);

        System.out.println("Italia: " + df.format(c.getTime()));
        System.out.println("Suica: " + df2.format(c.getTime()));
        System.out.println("India: " + df3.format(c.getTime()));
        System.out.println("Japao: " + df4.format(c.getTime()));

        System.out.println("Idioma de Itália para Japão: " + locItaly.getDisplayLanguage(locJapao));
        System.out.println("Idioma de Japão para Itália: " + locJapao.getDisplayLanguage(locItaly));
        System.out.println("País de Suíça para Itália: " + locSuica.getDisplayCountry(locItaly));
	}
```

```
Italia: lunedì 23 dicembre 2024
Suica: lunedì, 23 dicembre 2024
India: सोमवार, 23 दिसंबर 2024
Japao: 2024年12月23日月曜日
Idioma de Itália para Japão: イタリア語
Idioma de Japão para Itália: giapponese
País de Suíça para Itália: Svizzera
```

## Classes Utilitárias - Internacionalização Números com Locale e moeda com NumberFormat

```java
public static void main(String[] args) {
        float valor = 212.4567f;
        Locale locJP = Locale.of("jp");
        Locale locFR = Locale.of("fr");
        Locale locIT = Locale.of("it");
        NumberFormat[] nfa = new NumberFormat[4];
        nfa[0] = NumberFormat.getInstance();
        nfa[1] = NumberFormat.getInstance(locIT);
        nfa[2] = NumberFormat.getCurrencyInstance();
        nfa[3] = NumberFormat.getCurrencyInstance(locIT);
        for(NumberFormat nf : nfa){
            System.out.println(nf.format(valor));
        }
        NumberFormat nf = NumberFormat.getInstance();
        System.out.println(nf.getMaximumFractionDigits());
        nf.setMaximumFractionDigits(1);
        System.out.println(nf.format(valor));
        String valorString = "212,4567";
        try {
            System.out.println(nf.parse(valorString));
            nf.setParseIntegerOnly(true);
            System.out.println(nf.parse(valorString));
        } catch (ParseException e) {
            e.printStackTrace();
        }
	}
```

```
212,457
212,457
R$ 212,46
212,46 ¤
3
212,5
212.4567
212
```

## Classes Utilitárias - SimpleDateFormat

```java
public static void main(String[] args) {
    Calendar c = Calendar.getInstance();
    String mascara = "dd 'de' MMMM 'de' yyyy";
    SimpleDateFormat formatador = new SimpleDateFormat(mascara);
    System.out.println(formatador.format(c.getTime()));
	}
```

```
23 de dezembro de 2024
```

## Classes Utilitárias - LocalDate

**`EXEMPLO CHATGPT`**

```java
public class ExemploLocalDate {
    public static void main(String[] args) {
        // Obter a data atual
        LocalDate dataAtual = LocalDate.now();
        System.out.println("Data atual: " + dataAtual);
    }
}
```

```
Data atual: 2024-12-23
```

```java
public class ExemploLocalDate {
    public static void main(String[] args) {
        // Criar uma data específica
        LocalDate dataEspecifica = LocalDate.of(2024, 12, 25);
        System.out.println("Data específica: " + dataEspecifica);
    }
}
```

```
Data específica: 2024-12-25
```

## Classes Utilitárias - LocalTime

**`EXEMPLO CHATGPT`**

```java
public static void main(String[] args) {
    // Obter o horário atual
    LocalTime horarioAtual = LocalTime.now();
    System.out.println("Horário atual: " + horarioAtual);
}
```

```
Horário atual: 17:58:28.859071400
```

```java
public static void main(String[] args) {
        // Criar um horário específico
    LocalTime horarioEspecifico = LocalTime.of(14, 30); // 14:30
    System.out.println("Horário específico: " + horarioEspecifico);
}
```

```
Horário específico: 14:30
```

## Classes Utilitárias - LocalDateTime

**`EXEMPLO CHATGPT`**

```java
public class ExemploLocalDateTime {
    public static void main(String[] args) {
        // Obter a data e hora atual
        LocalDateTime dataHoraAtual = LocalDateTime.now();
        System.out.println("Data e hora atual: " + dataHoraAtual);
    }
}

```

```
Data e hora atual: 2024-12-23T18:01:48.381637300
```

```java
public class ExemploLocalDateTime {
    public static void main(String[] args) {
        // Criar uma data e hora específicas
        LocalDateTime dataHoraEspecifica = LocalDateTime.of(2024, 12, 31, 14, 30);
        System.out.println("Data e hora específica: " + dataHoraEspecifica);
    }
}

```

```
Data e hora específica: 2024-12-31T14:30
```

## Classes Utilitárias - Instant

- Representa um ponto específico na linha do tempo(em UTC), útil para medir instantes de tempo ou para converter entre diferentes fusos horários.
- A diferença é que trabalha com nanosegundos;

**`EXEMPLO CHATGPT`**

```java
import java.time.Instant;

public class ExemploInstant {
    public static void main(String[] args) {
        // Obter o instante atual
        Instant agora = Instant.now();
        System.out.println("Instante atual: " + agora);
    }
}
```

```
Instante atual: 2024-12-23T21:11:21.195585500Z
```

```java
public class ExemploInstant {
    public static void main(String[] args) {
        // Obter o instante atual
        Instant agora = Instant.now();

        // Converter Instant para LocalDateTime no fuso horário do sistema
        LocalDateTime dataHora = LocalDateTime.ofInstant(agora, ZoneId.systemDefault());
        System.out.println("Data e hora no fuso horário local: " + dataHora);
    }
}
```

```
Data e hora no fuso horário local: 2024-12-23T18:13:04.195444200
```

## Classes Utilitárias - Duration

```java
import java.time.Duration;
import java.time.Instant;

public class ExemploDuration {
    public static void main(String[] args) {
        Instant inicio = Instant.now();

        // Simula algum processamento
        try {
            Thread.sleep(2000); // Pausa por 2 segundos
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        Instant fim = Instant.now();

        // Calcular a duração entre os dois instantes
        Duration duracao = Duration.between(inicio, fim);

        System.out.println("Duração: " + duracao.toMillis() + " milissegundos");
    }
}
```

```
Duração: 2003 milissegundos
```

- Duration é ideal para medir a quantidade de tempo entre dois Instant, LocalTime ou LocalDateTime.
- Para períodos maiores (como semanas ou meses), é mais indicado usar a classe Period, que é específica para LocalDate.
- Duration lida com horas, minutos, segundos e nanossegundos.
