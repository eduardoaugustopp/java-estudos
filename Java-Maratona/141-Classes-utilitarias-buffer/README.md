## Classes Utilitárias - IO pt 04 - BufferedWriter

```java
public class BufferedTest {

    public static void main(String[] args) {
        File file = new File("file_buffered.txt");

        try(BufferedWriter bw = new BufferedWriter(new FileWriter(file));
            BufferedReader br = new BufferedReader(new FileReader(file))){

            bw.write("Escrevendo uma mensagem");
            bw.newLine();
            bw.write("Outra Linha 3");
            bw.flush();

            String s = null;
            while ((s = br.readLine()) != null){
                System.out.println(s);
            }

        }catch (IOException e){
            e.printStackTrace();
        }

        /*try{
            FileWriter fw = new FileWriter(file);
            BufferedWriter bw = new BufferedWriter(fw);

            bw.write("Escrevendo uma mensagem");
            bw.newLine();
            bw.write("Outra Linha 3");
            bw.flush();
            bw.close();

            FileReader fr = new FileReader(file);
            BufferedReader br = new BufferedReader(fr);
            String s = null;
            while ((s = br.readLine()) != null){
                System.out.println(s);
            }
            br.close();


        }catch(IOException e){
            e.printStackTrace();
        }*/

    }

}

```

```
Escrevendo uma mensagem
Outra Linha 3

```
