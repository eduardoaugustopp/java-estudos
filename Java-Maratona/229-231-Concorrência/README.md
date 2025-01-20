## Concorrência pt 01 - AtomicInteger

**`EXEMPLO CHATGPT`**

O uso do AtomicInteger em Java é essencial em situações de concorrência onde múltiplas threads precisam acessar e modificar uma mesma variável inteira. Ele é parte do pacote java.util.concurrent.atomic e fornece operações atômicas para variáveis inteiras, garantindo segurança e desempenho sem a necessidade de sincronização explícita.

Exemplo básico de uso

```java
public class ExemploAtomicInteger {
    private static final AtomicInteger contador = new AtomicInteger(0);

    public static void main(String[] args) {
        // Incrementa o valor atomicamente
        System.out.println("Valor inicial: " + contador.get());
        System.out.println("Incrementando: " + contador.incrementAndGet());
        System.out.println("Decrementando: " + contador.decrementAndGet());

        // Comparação atômica e atualização
        int esperado = 1;
        boolean atualizado = contador.compareAndSet(esperado, 10);
        System.out.println("Atualização foi bem-sucedida? " + atualizado);
        System.out.println("Valor final: " + contador.get());
    }
}

```

```
Valor inicial: 0
Incrementando: 1
Decrementando: 0
Atualização foi bem-sucedida? true
Valor final: 10
```

## Concorrência pt 02 - Lock and ReentrantLock

**`EXEMPLO CHATGPT`**

O uso de Lock e ReentrantLock em Java é um passo além do synchronized quando lidamos com concorrência. A classe ReentrantLock pertence ao pacote java.util.concurrent.locks e oferece mais controle e flexibilidade sobre o gerenciamento de locks em comparação com o synchronized.

Exemplo básico de ReentrantLock

```java
public class ExemploReentrantLock {
    private static final Lock lock = new ReentrantLock();
    private static int contador = 0;

    public static void main(String[] args) {
        Runnable tarefa = () -> {
            lock.lock(); // Adquirir o lock
            try {
                contador++;
                System.out.println(Thread.currentThread().getName() + " incrementou contador para: " + contador);
            } finally {
                lock.unlock(); // Sempre liberar o lock
            }
        };

        Thread thread1 = new Thread(tarefa);
        Thread thread2 = new Thread(tarefa);

        thread1.start();
        thread2.start();
    }
}

```

```
Thread-0 incrementou contador para: 1
Thread-1 incrementou contador para: 2
```

Exemplo com tryLock

```java
public class ExemploTryLock {
    private static final Lock lock = new ReentrantLock();

    public static void main(String[] args) {
        Runnable tarefa = () -> {
            try {
                if (lock.tryLock(2, TimeUnit.SECONDS)) {
                    try {
                        System.out.println(Thread.currentThread().getName() + " obteve o lock!");
                        Thread.sleep(1000); // Simula trabalho
                    } finally {
                        lock.unlock();
                        System.out.println(Thread.currentThread().getName() + " liberou o lock.");
                    }
                } else {
                    System.out.println(Thread.currentThread().getName() + " não conseguiu obter o lock.");
                }
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + " foi interrompida.");
            }
        };

        Thread thread1 = new Thread(tarefa);
        Thread thread2 = new Thread(tarefa);

        thread1.start();
        thread2.start();
    }
}

```

```
Thread-0 obteve o lock!
Thread-1 não conseguiu obter o lock.
Thread-0 liberou o lock.

```

Exemplo com Condition

O ReentrantLock suporta múltiplas condições por meio da interface Condition. Isso é útil quando precisamos de mais controle do que o oferecido por wait() e notify() do synchronized.

```java
public class ExemploCondition {
    private static final Lock lock = new ReentrantLock();
    private static final Condition condition = lock.newCondition();
    private static boolean pronto = false;

    public static void main(String[] args) {
        Thread produtor = new Thread(() -> {
            lock.lock();
            try {
                System.out.println("Produzindo item...");
                Thread.sleep(1000); // Simula trabalho
                pronto = true;
                condition.signal(); // Notifica a thread aguardando
                System.out.println("Item produzido!");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        });

        Thread consumidor = new Thread(() -> {
            lock.lock();
            try {
                while (!pronto) {
                    System.out.println("Aguardando item...");
                    condition.await(); // Aguarda até ser notificado
                }
                System.out.println("Consumindo item!");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        });

        consumidor.start();
        produtor.start();
    }
}

```

```
Aguardando item...
Produzindo item...
Item produzido!
Consumindo item!
```
