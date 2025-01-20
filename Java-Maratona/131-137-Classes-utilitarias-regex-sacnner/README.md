## Classes Utilitárias - Regex pt 01 - Pattern e Matcher

`Expressões regulares` - é uma linguagem que foi desenvolvida que utiliza método caractere, então utiliza simbolos, vai encontrar padrôes no texto; elas precisam ser compiladas `.compile()` para validar se essa expressão regular realmente funciona;

- Para trabalhar com `regex` precisa de duas classes:

1. - `Pattern` - que é o padrão;
2. - `Matcher` - que encontra aquele padrão;

```java
public class ExpressoesRegularesTest {
    public static void main(String[] args) {
        String regex = "aba";
        String texto = "ababababa";//temos 3 'aba' ele vai ignorar o da posição 2

        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(texto);

        System.out.println("Texto: " + texto);
        System.out.println("Indice: 0123456789 ");
        System.out.println("Expressão: " + matcher.pattern());
        System.out.println("Posições encontradas: ");
        while(matcher.find()){//o matcher vai ficar procurando durante o texto, vai pega 'aba' e enquanto ele achar 'aba' no texto
            System.out.print(matcher.start() + " ");// o start mostra a posicao que começo 'aba'
        }

    }
}

```

```
Texto: ababababa
Indice: 0123456789
Expressão: aba
Posições encontradas:
0 4
```

## Classes Utilitárias - Regex pt 02 - Pattern e Matcher - Meta caracteres

```java
public class ExpressoesRegularesTest {
    public static void main(String[] args) {

        /*
        *  \d - todos os dígitos
        *  \D - Tudo que não for dígito
        *  \s - espaços em branco \t \n \f \r
        *  \S - caracteres que naõ é branco
        *  \w - caracteres de palavras a-z A-Z, digitos e _
        *  \W - tudo o que não for caractere de palavra
        */

        String regex = "\\W";
        String texto = "0a  #$ ba1ba2baba345 ";

        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(texto);

        System.out.println("Texto: " + texto);
        System.out.println("Indice: 0123456789 ");
        System.out.println("Expressão: " + matcher.pattern());
        System.out.println("Posições encontradas: ");
        while(matcher.find()){
            System.out.print(matcher.start() + " ");
        }

    }
}

```

```
Texto: 0a  #$ ba1ba2baba345
Indice: 0123456789
Expressão: \W
Posições encontradas:
2 3 4 5 6 20
```

## Classes Utilitárias - Regex pt 03 - Pattern e Matcher - Range

```java
public class ExpressoesRegularesTest {
    public static void main(String[] args) {

        /*
        *  \d - todos os dígitos
        *  \D - Tudo que não for dígito
        *  \s - espaços em branco \t \n \f \r
        *  \S - caracteres que naõ é branco
        *  \w - caracteres de palavras a-z A-Z, digitos e _
        *  \W - tudo o que não for caractere de palavra
        *  [] - range representado por colchetes
        */

        String regex = "0[xX][0-9a-fA-F]";
        String texto = "'1 0x 0xF 0X10G 0x1";

        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(texto);

        System.out.println("Texto: " + texto);
        System.out.println("Indice: 0123456789 ");
        System.out.println("Expressão: " + matcher.pattern());
        System.out.println("Posições encontradas: ");
        while(matcher.find()){
            System.out.println(matcher.start() + " " + matcher.group());
        }

    }
}

```

```
Texto: '1 0x 0xF 0X10G 0x1
Indice: 0123456789
Expressão: 0[xX][0-9a-fA-F]
Posições encontradas:
6 0xF
10 0X1
16 0x1

```

## Classes Utilitárias - Regex pt 04 - Pattern e Matcher - Quantificadores pt 01

```java
public class ExpressoesRegularesTest {
    public static void main(String[] args) {

        /*
        *  \d - todos os dígitos
        *  \D - Tudo que não for dígito
        *  \s - espaços em branco \t \n \f \r
        *  \S - caracteres que naõ é branco
        *  \w - caracteres de palavras a-z A-Z, digitos e _
        *  \W - tudo o que não for caractere de palavra
        *  []
        *
        *  ? zero ou uma
        *  * zero ou mais
        *  + uma ou mais
        *  {n,m} de n até m
        *  ()
        *  |
        *  &
        *  o(v|c)o = ovo, oco
        *  maca(rr|c)ão = macarrão ou macação
        *
        */

        String regex = "0[xX]([0-9a-fA-F])+(\\s|$)";
        String texto = "12 0x 0X 0X01FFABC 0x10G 0x1";

        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(texto);

        System.out.println("Texto: " + texto);
        System.out.println("Indice: 0123456789 ");
        System.out.println("Expressão: " + matcher.pattern());
        System.out.println("Posições encontradas: ");
        while(matcher.find()){
            System.out.println(matcher.start() + " " + matcher.group());
        }

    }
}

```

```
Texto: 12 0x 0X 0X01FFABC 0x10G 0x1
Indice: 0123456789
Expressão: 0[xX]([0-9a-fA-F])+(\s|$)
Posições encontradas:
9 0X01FFABC
25 0x1
```

## Classes Utilitárias - Regex pt 05 - Pattern e Matcher - Quantificadores pt 02

```java
public class ExpressoesRegularesTest {
    public static void main(String[] args) {

        /*
        *  \d - todos os dígitos
        *  \D - Tudo que não for dígito
        *  \s - espaços em branco \t \n \f \r
        *  \S - caracteres que naõ é branco
        *  \w - caracteres de palavras a-z A-Z, digitos e _
        *  \W - tudo o que não for caractere de palavra
        *  []
        *
        *  ? zero ou uma
        *  * zero ou mais
        *  + uma ou mais
        *  {n,m} de n até m
        *  ()
        *  |
        *  &
        *  o(v|c)o = ovo, oco
        *  maca(rr|c)ão = macarrão ou macação
        *
        */

//        String regex = "0[xX]([0-9a-fA-F])+(\\s|$)";
//        String texto = "12 0x 0X 0X01FFABC 0x10G 0x1";

        //String regex = "([a-zA-Z0-9\\._-])+@([a-zA-Z])+(\\.([a-zA-Z])+)+"; //([\\w\\.])+@([a-zA-Z])+(\.([a-zA-Z])+)+
        //String texto = "fulano@hotmail.com, 123abc@gmail.com, @#te@gmail.com, teste@teste.com.br, teste@teste.com, teste@mail";

        String regex = "\\d{2}/\\d{2}/\\d{2,4}";
        String texto = "05/10/2010 05/05/2015 1/1/01 01/05/95"; // dd/MM/yyyy

        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(texto);

        System.out.println("Texto: " + texto);
        System.out.println("Indice: 0123456789 ");
        System.out.println("Expressão: " + matcher.pattern());
        System.out.println("Posições encontradas: ");
        while(matcher.find()){
            System.out.println(matcher.start() + " " + matcher.group());
        }

    }
}

```

```
Texto: 05/10/2010 05/05/2015 1/1/01 01/05/95
Indice: 0123456789
Expressão: \d{2}/\d{2}/\d{2,4}
Posições encontradas:
0 05/10/2010
11 05/05/2015
29 01/05/95
```

## Classes Utilitárias - Regex pt 06 - Pattern e Matcher - Anchor

https://regexr.com/

```java
//  ^ - negação
        String regex = "proj([^,])*";
        String texto = "proj1.bkp, proj1.java, proj1.class, proj1final.java, proj2.bkp, proj3.java, diagrama, texto, foto";

```

```
Texto: proj1.bkp, proj1.java, proj1.class, proj1final.java, proj2.bkp, proj3.java, diagrama, texto, foto
Indice: 0123456789
Expressão: proj([^,])*
Posições encontradas:
0 proj1.bkp
11 proj1.java
23 proj1.class
36 proj1final.java
53 proj2.bkp
64 proj3.java
```

## Classes Utilitárias - Scanner - Tokens e Delimitadores

```java
public class TokenTeste {
    public static void main(String[] args) {
        String str = "José Malcher Joanna Camila Anna John Matheus junior";
        String[] tokens = str.split(" ");
        for(String arr: tokens){
            System.out.println(arr.trim());
        }
    }
}

```

```
José
Malcher
Joanna
Camila
Anna
John
Matheus
junior
```

```java
public class ScannerTeste {
    public static void main(String[] args) {

        Scanner scanner = new Scanner("1 true 100 oi");

        while (scanner.hasNext()) {
            System.out.println(scanner.next());
        }

        System.out.println("####################");

        Scanner scanner2 = new Scanner("1 true 100 oi");

        while (scanner2.hasNext()) {
            if (scanner2.hasNextInt()) {
                int i = scanner2.nextInt();
                System.out.println("int " + i);
            } else if (scanner2.hasNextBoolean()) {
                boolean b = scanner2.nextBoolean();
                System.out.println(b);
            } else {
                System.out.println(scanner2.next());
            }
        }
    }
}

```

```
1
true
100
oi
####################
int 1
true
int 100
oi
`
```
