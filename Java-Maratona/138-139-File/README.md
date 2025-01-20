## 138 - Classes Utilitárias - IO pt 01 - File

- Para criar o arqivo preciso chamar `file.createNewFile` esse é um método que vai retornar um valor booleano dizendo se aquele arquivo foi criado ou não, ele só será criado se ele não existir e que também precisa de um tratamento de exceção (Ctrl + 1 -> Surround Try/Catch) depois (Ctrl + 1 -> new local variable);

```java
File file = new File("Arquivo.txt");
        try {
            // Cria o arquivo e verifica se foi criado
            System.out.println("Arquivo criado? " + file.createNewFile());

            // Verifica se o arquivo existe
            boolean exists = file.exists();
            System.out.println("Permissão de leitura? " + file.canRead());
            System.out.println("Caminho relativo: " + file.getPath());
            System.out.println("Caminho absoluto: " + file.getAbsolutePath());
            System.out.println("É diretório? " + file.isDirectory());
            System.out.println("Está oculto? " + file.isHidden());
            System.out.println("Última modificação: " + new Date(file.lastModified()));

            // Deleta o arquivo se ele existe
            if (exists) {
                System.out.println("Deletado? " + file.delete());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

        SAÍDA:

                Arquivo criado? false
                Permissão de leitura? true
                Caminho relativo: Arquivo.txt
                Caminho absoluto: C:\Users\eduar\eclipse-workspace\Wio\Teste\Arquivo.txt
                É diretório? false
                Está oculto? false
                Última modificação: Mon Nov 25 10:10:30 BRT 2024
                Deletado? true
```

## 139 - Classes Utilitárias - IO pt 02 - FileWriter

- A classe FileWriter e FileReader servem para somente escrever e ler caracteres em um arquivo;
- Quando esta trabalhando com `IO` começa encadear diversas classes;
- Somente de criar o objeto já criamos o arquivo;

```
File file = new File("Arquivo.txt");
```

`FileWriter fw = new FileWriter(file);` - (Ctrl + 1 -> Surround Try/Catch);

- Toda vez que você escreve em um arquivo, está trabalhando com um `buffer`. Isso significa que você utiliza um recurso do sistema operacional, pois o arquivo está localizado no HD, e o sistema operacional cria um `túnel` para que o Java possa gravar os dados no arquivo (por exemplo, um arquivo.txt). Nesse túnel, chamado de `stream`, você pode realizar várias operações de escrita. Porém, durante esse processo, pode acontecer de algumas informações permanecerem no buffer, sem serem efetivamente gravadas no arquivo. Para garantir que todos os dados sejam transferidos do buffer para o arquivo, é necessário utilizar o `método flush`. Ele força a saída de todos os dados que estão no buffer para o arquivo. Após isso, o método `close` deve ser chamado para liberar o recurso e encerrar a conexão com o arquivo.
- Esse exemplo a seguir, toda vez que eu alterar a mensagem e roda vai sobrescrever:

```java
		File file = new File("Arquivo.txt");
		try {
			FileWriter fw = new FileWriter(file);

			fw.write("Escrevendo uma mensagem no arquivo\nE pulando uma linha 3");
			fw.flush();
			fw.close();

		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

```

- O `método append` permite incluir novos dados ao final do arquivo em vez de sobrescrever seu conteúdo. Para isso, é necessário configurá-lo no construtor do objeto usado para manipular o arquivo. Quando ativado, ele adiciona os dados ao final do arquivo existente, preservando o conteúdo já salvo. **, true**

```java
FileWriter fw = new FileWriter(file, true);
```

- `fr.read` ele vai retornar um int e vai colocar dentro de um array; cria um array char;

```java
public class FileWriterReaderTest {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		File file = new File("Arquivo.txt");
		try {
			FileWriter fw = new FileWriter(file, true);

			fw.write("Escrevendo uma mensagem no arquivo\nE pulando uma linha 5");
			fw.flush();
			fw.close();

			FileReader fr = new FileReader(file);
			char[] in = new char[500];
			int size = fr.read(in);
			System.out.println("tamanho " + size);
				for(char c: in) {
					System.out.print(c);
				}

			fr.close();


		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

	}

}
            SAÍDA:  tamanho 168
                    Escrevendo uma mensagem no arquivo
                    E pulando uma linha 3Escrevendo uma mensagem no arquivo
                    E pulando uma linha 5Escrevendo uma mensagem no arquivo
                    E pulando uma linha 5

```
