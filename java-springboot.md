# Treinamento Java e Spring Boot: do Básico ao Avançado

> Material de apoio para um programa de treinamento completo. Os exemplos usam **Java 21 (LTS)** e **Spring Boot 3.x**.

## Sumário

**Parte 1: Java**
1. [Fundamentos da linguagem](#módulo-1--fundamentos-da-linguagem)
2. [Programação Orientada a Objetos](#módulo-2--programação-orientada-a-objetos)
3. [Java intermediário](#módulo-3--java-intermediário)
4. [Java moderno (8 a 21)](#módulo-4--java-moderno-8-a-21)
5. [Java avançado](#módulo-5--java-avançado)

**Parte 2: Spring Boot**
6. [Primeiros passos](#módulo-6--primeiros-passos-com-spring-boot)
7. [API REST](#módulo-7--api-rest)
8. [Persistência com Spring Data JPA](#módulo-8--persistência-com-spring-data-jpa)
9. [Configuração, profiles e arquitetura](#módulo-9--configuração-profiles-e-arquitetura)
10. [Spring Security e JWT](#módulo-10--spring-security-e-jwt)
11. [Testes](#módulo-11--testes)
12. [Spring Boot avançado](#módulo-12--spring-boot-avançado)
13. [Microsserviços e produção](#módulo-13--microsserviços-e-produção)
14. [Plano de estudos e projeto final](#plano-de-estudos-sugerido)

---

# PARTE 1: JAVA

## Módulo 1 · Fundamentos da linguagem

### 1.1 Como o Java funciona

```
Código-fonte (.java) → javac → Bytecode (.class) → JVM → Código de máquina
```

- **JDK**: kit de desenvolvimento (compilador + ferramentas + JRE).
- **JRE**: ambiente de execução (JVM + bibliotecas).
- **JVM**: máquina virtual que executa o bytecode, o que torna o Java portável ("write once, run anywhere").

### 1.2 Primeiro programa

```java
public class OlaMundo {
    public static void main(String[] args) {
        System.out.println("Olá, mundo!");
    }
}
```

Compilar e executar:

```bash
javac OlaMundo.java
java OlaMundo
# Java 11+ permite executar direto:
java OlaMundo.java
```

### 1.3 Tipos primitivos e variáveis

| Tipo | Tamanho | Exemplo |
|------|---------|---------|
| `byte` | 8 bits | `byte b = 10;` |
| `short` | 16 bits | `short s = 1000;` |
| `int` | 32 bits | `int i = 42;` |
| `long` | 64 bits | `long l = 10_000_000_000L;` |
| `float` | 32 bits | `float f = 3.14f;` |
| `double` | 64 bits | `double d = 3.14159;` |
| `char` | 16 bits | `char c = 'A';` |
| `boolean` | 1 bit lógico | `boolean ok = true;` |

```java
int idade = 30;
double salario = 5500.50;
boolean ativo = true;
char inicial = 'J';

// Inferência de tipo (Java 10+)
var nome = "Maria";          // String
var numeros = List.of(1, 2); // List<Integer>

// Constantes
final double PI = 3.14159;

// Conversões (casting)
int x = (int) 9.99;   // 9 (narrowing, explícito)
double y = 10;        // 10.0 (widening, implícito)
```

**Wrappers** (`Integer`, `Double`, `Boolean`...) permitem usar primitivos como objetos, em coleções por exemplo. O *autoboxing* faz a conversão automática:

```java
Integer boxed = 10;   // autoboxing
int primitivo = boxed; // unboxing
```

### 1.4 Operadores

```java
int a = 10, b = 3;
System.out.println(a + b);   // 13
System.out.println(a / b);   // 3  (divisão inteira)
System.out.println(a % b);   // 1  (resto)
System.out.println((double) a / b); // 3.333...

a++;  // incremento
a += 5; // a = a + 5

// Comparação e lógicos
boolean r = (a > b) && (b != 0) || !(a == 5);

// Ternário
String status = a > 10 ? "grande" : "pequeno";
```

### 1.5 Estruturas de controle

```java
// if / else if / else
int nota = 75;
if (nota >= 90) {
    System.out.println("A");
} else if (nota >= 70) {
    System.out.println("B");
} else {
    System.out.println("C");
}

// switch tradicional
switch (nota / 10) {
    case 10, 9 -> System.out.println("Excelente");
    case 8, 7  -> System.out.println("Bom");
    default    -> System.out.println("Precisa melhorar");
}

// switch como expressão (Java 14+)
String conceito = switch (nota / 10) {
    case 10, 9 -> "A";
    case 8, 7 -> "B";
    default -> "C";
};

// for
for (int i = 0; i < 5; i++) {
    System.out.println("i = " + i);
}

// for-each
int[] valores = {1, 2, 3};
for (int v : valores) {
    System.out.println(v);
}

// while e do-while
int n = 0;
while (n < 3) { n++; }
do { n--; } while (n > 0);

// break e continue
for (int i = 0; i < 10; i++) {
    if (i == 3) continue;
    if (i == 6) break;
}
```

### 1.6 Arrays

```java
int[] numeros = new int[5];          // [0, 0, 0, 0, 0]
String[] nomes = {"Ana", "Bruno", "Carla"};
int[][] matriz = {{1, 2}, {3, 4}};

System.out.println(nomes.length);    // 3
System.out.println(matriz[1][0]);    // 3

// Utilitários
import java.util.Arrays;
Arrays.sort(numeros);
System.out.println(Arrays.toString(nomes));
int[] copia = Arrays.copyOf(numeros, 10);
```

### 1.7 Strings

Strings são **imutáveis**. Cada "modificação" cria um novo objeto.

```java
String s = "Java";
System.out.println(s.length());
System.out.println(s.toUpperCase());
System.out.println(s.charAt(0));
System.out.println(s.substring(1, 3));  // "av"
System.out.println(s.contains("av"));
System.out.println("a,b,c".split(",").length); // 3

// Comparação: use equals, nunca ==
String a = new String("x");
String b = new String("x");
System.out.println(a == b);       // false (referências)
System.out.println(a.equals(b));  // true  (conteúdo)

// Concatenação eficiente em loops
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 5; i++) sb.append(i).append(",");
System.out.println(sb);

// Formatação
String msg = String.format("Olá, %s! Você tem %d anos.", "Ana", 30);

// Text blocks (Java 15+)
String json = """
    {
      "nome": "Ana",
      "idade": 30
    }
    """;
```

### 1.8 Métodos

```java
public class Calculadora {

    public static int somar(int a, int b) {
        return a + b;
    }

    // Sobrecarga (overload): mesmo nome, parâmetros diferentes
    public static double somar(double a, double b) {
        return a + b;
    }

    // Varargs
    public static int somarTodos(int... numeros) {
        int total = 0;
        for (int n : numeros) total += n;
        return total;
    }

    public static void main(String[] args) {
        System.out.println(somar(2, 3));
        System.out.println(somarTodos(1, 2, 3, 4));
    }
}
```

> Java sempre passa argumentos **por valor**. Para objetos, o valor copiado é a *referência*.

### 1.9 Entrada de dados

```java
import java.util.Scanner;

try (Scanner sc = new Scanner(System.in)) {
    System.out.print("Seu nome: ");
    String nome = sc.nextLine();
    System.out.print("Sua idade: ");
    int idade = sc.nextInt();
    System.out.println(nome + " tem " + idade + " anos.");
}
```

### Exercícios do Módulo 1
1. Calcule o fatorial de um número.
2. Imprima os primeiros 20 números da sequência de Fibonacci.
3. Verifique se uma string é um palíndromo.
4. Ordene um array sem usar `Arrays.sort`.

---

## Módulo 2 · Programação Orientada a Objetos

### 2.1 Classes e objetos

```java
public class Pessoa {
    // Atributos (estado)
    private String nome;
    private int idade;

    // Construtor
    public Pessoa(String nome, int idade) {
        this.nome = nome;
        this.idade = idade;
    }

    // Métodos (comportamento)
    public void apresentar() {
        System.out.println("Olá, sou " + nome + " e tenho " + idade + " anos.");
    }

    // Getters e setters (encapsulamento)
    public String getNome() { return nome; }

    public void setIdade(int idade) {
        if (idade < 0) throw new IllegalArgumentException("Idade inválida");
        this.idade = idade;
    }
}

Pessoa p = new Pessoa("Ana", 30);
p.apresentar();
```

### 2.2 Os quatro pilares

**Encapsulamento**: esconda o estado interno e exponha apenas o necessário (`private` + métodos públicos).

**Herança**: reaproveita e especializa comportamento.

```java
public class Animal {
    protected String nome;

    public Animal(String nome) { this.nome = nome; }

    public void emitirSom() {
        System.out.println("...");
    }
}

public class Cachorro extends Animal {
    public Cachorro(String nome) { super(nome); }

    @Override
    public void emitirSom() {
        System.out.println(nome + " diz: Au au!");
    }
}
```

**Polimorfismo**: a mesma chamada, comportamentos diferentes.

```java
List<Animal> animais = List.of(new Cachorro("Rex"), new Gato("Mimi"));
for (Animal a : animais) {
    a.emitirSom();  // cada objeto responde do seu jeito
}
```

**Abstração**: modele o "o quê" sem detalhar o "como".

```java
public abstract class Forma {
    public abstract double area();

    public void descrever() {
        System.out.printf("Área: %.2f%n", area());
    }
}

public class Circulo extends Forma {
    private final double raio;
    public Circulo(double raio) { this.raio = raio; }

    @Override
    public double area() { return Math.PI * raio * raio; }
}
```

### 2.3 Interfaces

```java
public interface Pagavel {
    double calcularValor();

    // Método default (Java 8+)
    default String formatar() {
        return String.format("R$ %.2f", calcularValor());
    }

    // Método estático
    static Pagavel gratis() { return () -> 0.0; }
}

public class Fatura implements Pagavel {
    @Override
    public double calcularValor() { return 199.90; }
}
```

**Classe abstrata vs interface**: use classe abstrata quando há estado e código compartilhado entre subclasses relacionadas. Use interface para definir um contrato que classes de hierarquias diferentes podem cumprir.

### 2.4 Modificadores de acesso

| Modificador | Classe | Pacote | Subclasse | Qualquer lugar |
|-------------|:------:|:------:|:---------:|:--------------:|
| `private` | ✔ | | | |
| *(padrão)* | ✔ | ✔ | | |
| `protected` | ✔ | ✔ | ✔ | |
| `public` | ✔ | ✔ | ✔ | ✔ |

### 2.5 `static`, `final` e `this`

```java
public class Contador {
    private static int total = 0;      // compartilhado entre todas as instâncias
    public static final int MAX = 100; // constante

    public Contador() { total++; }
    public static int getTotal() { return total; }
}
```

- `final` em variável: não pode ser reatribuída.
- `final` em método: não pode ser sobrescrito.
- `final` em classe: não pode ser estendida.

### 2.6 `equals`, `hashCode` e `toString`

```java
import java.util.Objects;

public class Produto {
    private final String codigo;
    private final String nome;

    public Produto(String codigo, String nome) {
        this.codigo = codigo;
        this.nome = nome;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Produto p)) return false;
        return Objects.equals(codigo, p.codigo);
    }

    @Override
    public int hashCode() { return Objects.hash(codigo); }

    @Override
    public String toString() { return "Produto{" + codigo + ", " + nome + "}"; }
}
```

> **Contrato**: se `a.equals(b)`, então `a.hashCode() == b.hashCode()`. Quebrar isso causa bugs sutis em `HashMap` e `HashSet`.

### 2.7 Enums

```java
public enum Status {
    PENDENTE("Pendente"),
    APROVADO("Aprovado"),
    CANCELADO("Cancelado");

    private final String descricao;

    Status(String descricao) { this.descricao = descricao; }
    public String getDescricao() { return descricao; }
}

Status s = Status.valueOf("APROVADO");
for (Status st : Status.values()) System.out.println(st);
```

### 2.8 Classes internas e anônimas

```java
public class Externa {
    private int valor = 10;

    class Interna {                      // acessa membros da externa
        int dobro() { return valor * 2; }
    }

    static class Aninhada { }            // não acessa membros de instância
}

Runnable r = new Runnable() {            // classe anônima
    @Override public void run() { System.out.println("rodando"); }
};
```

### 2.9 Princípios SOLID (resumo)

| Princípio | Ideia |
|-----------|-------|
| **S**ingle Responsibility | Uma classe, uma razão para mudar |
| **O**pen/Closed | Aberta para extensão, fechada para modificação |
| **L**iskov Substitution | Subtipos devem ser substituíveis pelos tipos base |
| **I**nterface Segregation | Interfaces pequenas e específicas |
| **D**ependency Inversion | Dependa de abstrações, não de implementações |

```java
// Dependency Inversion: a classe depende da interface, não da implementação
public interface Notificador { void enviar(String msg); }

public class EmailNotificador implements Notificador {
    public void enviar(String msg) { System.out.println("Email: " + msg); }
}

public class PedidoService {
    private final Notificador notificador;

    public PedidoService(Notificador notificador) {   // injeção via construtor
        this.notificador = notificador;
    }

    public void finalizar() { notificador.enviar("Pedido finalizado"); }
}
```

> Esse último exemplo é a base do que o Spring faz por você: a **Injeção de Dependências**.

---

## Módulo 3 · Java intermediário

### 3.1 Tratamento de exceções

```
Throwable
├── Error                  (problemas da JVM; não trate)
└── Exception
    ├── RuntimeException   (unchecked: não exige try/catch)
    └── demais             (checked: o compilador exige tratamento)
```

```java
public class SaldoInsuficienteException extends Exception {
    public SaldoInsuficienteException(String msg) { super(msg); }
}

public class Conta {
    private double saldo;

    public void sacar(double valor) throws SaldoInsuficienteException {
        if (valor > saldo) {
            throw new SaldoInsuficienteException("Saldo insuficiente: " + saldo);
        }
        saldo -= valor;
    }
}

try {
    conta.sacar(500);
} catch (SaldoInsuficienteException e) {
    System.err.println(e.getMessage());
} finally {
    System.out.println("Sempre executa");
}

// try-with-resources: fecha recursos AutoCloseable automaticamente
try (var reader = new java.io.BufferedReader(new java.io.FileReader("dados.txt"))) {
    System.out.println(reader.readLine());
} catch (java.io.IOException e) {
    e.printStackTrace();
}
```

**Boas práticas**: não engula exceções (`catch` vazio), capture o tipo mais específico, e use exceções de runtime para erros de programação ou de domínio.

### 3.2 Collections Framework

```
Iterable → Collection ┬→ List  (ArrayList, LinkedList)
                      ├→ Set   (HashSet, LinkedHashSet, TreeSet)
                      └→ Queue (ArrayDeque, PriorityQueue)
Map (HashMap, LinkedHashMap, TreeMap): não estende Collection
```

```java
// List: ordenada, aceita duplicados
List<String> nomes = new ArrayList<>();
nomes.add("Ana");
nomes.add("Bruno");
nomes.get(0);
nomes.remove("Ana");

// Set: sem duplicados
Set<Integer> unicos = new HashSet<>(List.of(1, 2, 2, 3));
System.out.println(unicos.size()); // 3

// Map: chave → valor
Map<String, Integer> idades = new HashMap<>();
idades.put("Ana", 30);
idades.putIfAbsent("Bruno", 25);
idades.merge("Ana", 1, Integer::sum);   // 31
for (Map.Entry<String, Integer> e : idades.entrySet()) {
    System.out.println(e.getKey() + " = " + e.getValue());
}

// Coleções imutáveis (Java 9+)
List<String> fixa = List.of("a", "b");
Map<String, Integer> mapa = Map.of("um", 1, "dois", 2);

// Ordenação
nomes.sort(Comparator.naturalOrder());
pessoas.sort(Comparator.comparing(Pessoa::getNome)
                       .thenComparing(Pessoa::getIdade, Comparator.reverseOrder()));
```

**Quando usar cada uma**

| Estrutura | Use quando | Complexidade típica |
|-----------|-----------|---------------------|
| `ArrayList` | Acesso por índice e leitura frequente | get O(1), add O(1) amortizado |
| `LinkedList` | Inserções/remoções no início/meio (raro na prática) | get O(n) |
| `HashMap` | Busca por chave | O(1) |
| `TreeMap` | Chaves ordenadas | O(log n) |
| `ArrayDeque` | Pilha ou fila | O(1) |

### 3.3 Generics

```java
public class Caixa<T> {
    private T conteudo;
    public void guardar(T item) { this.conteudo = item; }
    public T pegar() { return conteudo; }
}

Caixa<String> c = new Caixa<>();
c.guardar("texto");

// Métodos genéricos com limite superior
public static <T extends Comparable<T>> T maior(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}

// Wildcards
double somar(List<? extends Number> lista) {      // produtor (leitura)
    return lista.stream().mapToDouble(Number::doubleValue).sum();
}
void preencher(List<? super Integer> lista) {     // consumidor (escrita)
    lista.add(1);
}
```

> Regra **PECS**: *Producer Extends, Consumer Super*.

### 3.4 Lambdas e interfaces funcionais

```java
// Interface funcional: possui um único método abstrato
@FunctionalInterface
interface Operacao { int aplicar(int a, int b); }

Operacao soma = (a, b) -> a + b;
System.out.println(soma.aplicar(2, 3));

// Interfaces prontas de java.util.function
Predicate<String> vazia = String::isEmpty;
Function<String, Integer> tamanho = String::length;
Consumer<String> imprimir = System.out::println;
Supplier<Double> aleatorio = Math::random;
BiFunction<Integer, Integer, Integer> mult = (a, b) -> a * b;
UnaryOperator<String> maiuscula = String::toUpperCase;
```

### 3.5 Streams API

```java
record Funcionario(String nome, String depto, double salario) {}

List<Funcionario> funcs = List.of(
    new Funcionario("Ana", "TI", 8000),
    new Funcionario("Bruno", "TI", 6000),
    new Funcionario("Carla", "RH", 5000),
    new Funcionario("Diego", "RH", 4500)
);

// filter + map + collect
List<String> nomesTI = funcs.stream()
    .filter(f -> f.depto().equals("TI"))
    .map(Funcionario::nome)
    .toList();

// Agregações
double media = funcs.stream().mapToDouble(Funcionario::salario).average().orElse(0);

// Agrupamento
Map<String, List<Funcionario>> porDepto =
    funcs.stream().collect(Collectors.groupingBy(Funcionario::depto));

// Agrupar e somar
Map<String, Double> folha = funcs.stream()
    .collect(Collectors.groupingBy(Funcionario::depto,
             Collectors.summingDouble(Funcionario::salario)));

// Ordenar, limitar, achar
Optional<Funcionario> maisBemPago = funcs.stream()
    .max(Comparator.comparingDouble(Funcionario::salario));

// flatMap
List<List<Integer>> listas = List.of(List.of(1, 2), List.of(3));
List<Integer> plana = listas.stream().flatMap(List::stream).toList();

// Streams paralelos (use com cautela e meça antes)
long total = IntStream.rangeClosed(1, 1_000_000).parallel().filter(n -> n % 2 == 0).count();
```

### 3.6 Optional

```java
Optional<String> opt = Optional.ofNullable(buscarNome());

String nome = opt.orElse("Desconhecido");
String outro = opt.map(String::toUpperCase).orElseGet(() -> "N/A");
opt.ifPresent(System.out::println);
String obrigatorio = opt.orElseThrow(() -> new IllegalStateException("Nome ausente"));
```

> Use `Optional` como **tipo de retorno**. Evite-o em atributos de classe e parâmetros de método.

### 3.7 Datas e horas (`java.time`)

```java
LocalDate hoje = LocalDate.now();
LocalDateTime agora = LocalDateTime.now();
LocalDate natal = LocalDate.of(2026, 12, 25);

long dias = ChronoUnit.DAYS.between(hoje, natal);
LocalDate amanha = hoje.plusDays(1);

DateTimeFormatter fmt = DateTimeFormatter.ofPattern("dd/MM/yyyy HH:mm");
String texto = agora.format(fmt);

ZonedDateTime sp = ZonedDateTime.now(ZoneId.of("America/Sao_Paulo"));
Instant instante = Instant.now();  // ponto na linha do tempo (UTC)
Duration d = Duration.ofMinutes(90);
```

### 3.8 Arquivos (I/O com NIO.2)

```java
Path caminho = Path.of("dados.txt");

Files.writeString(caminho, "linha 1\nlinha 2\n");
String conteudo = Files.readString(caminho);
List<String> linhas = Files.readAllLines(caminho);

// Arquivos grandes: leia com stream (lazy)
try (Stream<String> stream = Files.lines(caminho)) {
    stream.filter(l -> l.contains("1")).forEach(System.out::println);
}

Files.exists(caminho);
Files.createDirectories(Path.of("saida/logs"));
Files.copy(caminho, Path.of("copia.txt"), StandardCopyOption.REPLACE_EXISTING);
```

### 3.9 Maven e Gradle

Gerenciadores de dependências e build.

```xml
<!-- pom.xml (Maven) -->
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.exemplo</groupId>
  <artifactId>meu-app</artifactId>
  <version>1.0.0</version>

  <properties>
    <maven.compiler.release>21</maven.compiler.release>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.10.2</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

```bash
mvn clean package     # compila, testa e empacota
mvn test              # roda testes
mvn dependency:tree   # mostra a árvore de dependências
```

### 3.10 Testes unitários com JUnit 5

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import static org.junit.jupiter.api.Assertions.*;

class CalculadoraTest {

    @Test
    void deveSomarDoisNumeros() {
        assertEquals(5, Calculadora.somar(2, 3));
    }

    @ParameterizedTest
    @CsvSource({"1,1,2", "2,3,5", "-1,1,0"})
    void deveSomarVariosCasos(int a, int b, int esperado) {
        assertEquals(esperado, Calculadora.somar(a, b));
    }

    @Test
    void deveLancarExcecao() {
        var ex = assertThrows(IllegalArgumentException.class,
                () -> new Pessoa("Ana", 30).setIdade(-1));
        assertEquals("Idade inválida", ex.getMessage());
    }
}
```

---

## Módulo 4 · Java moderno (8 a 21)

### 4.1 Records (Java 16)

Classes imutáveis e concisas para transportar dados. O compilador gera construtor, getters, `equals`, `hashCode` e `toString`.

```java
public record Endereco(String rua, String cidade) {}

public record Usuario(String nome, int idade, Endereco endereco) {

    // Construtor compacto: validação
    public Usuario {
        if (idade < 0) throw new IllegalArgumentException("idade inválida");
    }

    // Métodos adicionais
    public boolean maiorDeIdade() { return idade >= 18; }
}

var u = new Usuario("Ana", 30, new Endereco("Rua A", "SP"));
System.out.println(u.nome());
```

### 4.2 Sealed classes (Java 17)

Restringem quem pode estender uma classe ou interface.

```java
public sealed interface Pagamento permits Cartao, Pix, Boleto {}

public record Cartao(String numero) implements Pagamento {}
public record Pix(String chave) implements Pagamento {}
public record Boleto(String codigoBarras) implements Pagamento {}
```

### 4.3 Pattern matching (Java 16 a 21)

```java
// instanceof com pattern
if (obj instanceof String s && s.length() > 3) {
    System.out.println(s.toUpperCase());
}

// switch com patterns e sealed types: o compilador garante exaustividade
String processar(Pagamento p) {
    return switch (p) {
        case Cartao c  -> "Cartão final " + c.numero().substring(c.numero().length() - 4);
        case Pix px    -> "Pix para " + px.chave();
        case Boleto b  -> "Boleto " + b.codigoBarras();
    };
}

// Record patterns (Java 21)
record Ponto(int x, int y) {}
Object o = new Ponto(3, 4);
if (o instanceof Ponto(int x, int y)) {
    System.out.println(x + y);
}

// Guards com "when"
String classificar(Object o) {
    return switch (o) {
        case Integer i when i > 100 -> "inteiro grande";
        case Integer i              -> "inteiro";
        case String s               -> "texto de " + s.length();
        case null                   -> "nulo";
        default                     -> "outro";
    };
}
```

### 4.4 Outras melhorias úteis

```java
// Sequenced Collections (Java 21)
List<Integer> lista = new ArrayList<>(List.of(1, 2, 3));
lista.getFirst();   // 1
lista.getLast();    // 3
lista.reversed();   // [3, 2, 1]

// String: novos métodos
"  texto ".strip();
"abc".repeat(3);
"".isBlank();
"a\nb".lines().count();

// HttpClient nativo (Java 11+)
HttpClient client = HttpClient.newHttpClient();
HttpRequest req = HttpRequest.newBuilder(URI.create("https://api.exemplo.com/dados")).build();
HttpResponse<String> resp = client.send(req, HttpResponse.BodyHandlers.ofString());
```

---

## Módulo 5 · Java avançado

### 5.1 Concorrência: threads

```java
// Thread básica
Thread t = new Thread(() -> System.out.println("Em outra thread"));
t.start();
t.join();

// ExecutorService: pool de threads (preferido)
try (ExecutorService executor = Executors.newFixedThreadPool(4)) {
    Future<Integer> futuro = executor.submit(() -> {
        Thread.sleep(500);
        return 42;
    });
    System.out.println(futuro.get());
}
```

**Condição de corrida e sincronização**

```java
public class Contador {
    private int valor;

    public synchronized void incrementar() { valor++; }  // exclusão mútua
}

// Alternativas mais flexíveis e performáticas
AtomicInteger atomico = new AtomicInteger();
atomico.incrementAndGet();

ReentrantLock lock = new ReentrantLock();
lock.lock();
try { /* seção crítica */ } finally { lock.unlock(); }

// Coleções thread-safe
ConcurrentHashMap<String, Integer> mapa = new ConcurrentHashMap<>();
mapa.merge("chave", 1, Integer::sum);
```

### 5.2 `CompletableFuture`: programação assíncrona

```java
CompletableFuture<String> usuario = CompletableFuture.supplyAsync(() -> buscarUsuario(1));
CompletableFuture<String> pedidos = CompletableFuture.supplyAsync(() -> buscarPedidos(1));

String resultado = usuario
        .thenCombine(pedidos, (u, p) -> u + " tem " + p)
        .exceptionally(ex -> "Falha: " + ex.getMessage())
        .join();

// Aguardar vários
CompletableFuture.allOf(usuario, pedidos).join();
```

### 5.3 Virtual Threads (Java 21)

Threads leves gerenciadas pela JVM. Permitem milhões de tarefas bloqueantes com código síncrono simples.

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 10_000; i++) {
        executor.submit(() -> {
            Thread.sleep(Duration.ofSeconds(1));  // bloqueio barato
            return "ok";
        });
    }
}  // aguarda todas terminarem

Thread.startVirtualThread(() -> System.out.println("virtual!"));
```

> Ideais para I/O (HTTP, banco). Para tarefas intensivas de CPU, continue usando pool de threads de plataforma.

### 5.4 Modelo de memória e Garbage Collector

```
Heap
├── Young Generation (Eden, Survivor 0/1)  → objetos novos
└── Old Generation                          → objetos de longa vida
Metaspace    → metadados de classes
Stack        → uma por thread: frames de métodos, variáveis locais
```

- **Stack**: variáveis locais e referências. Liberada ao sair do método.
- **Heap**: objetos. Limpo pelo GC.
- **Vazamento de memória**: acontece quando referências permanecem vivas sem necessidade (caches estáticos, listeners não removidos, `ThreadLocal`).

```bash
# Flags úteis
java -Xms512m -Xmx2g -XX:+UseG1GC -jar app.jar
java -XX:+UseZGC -jar app.jar              # baixa latência
java -Xlog:gc*:file=gc.log -jar app.jar    # log de GC

# Ferramentas de diagnóstico
jcmd <pid> GC.heap_info
jstack <pid>                  # dump de threads
jmap -dump:live,file=heap.hprof <pid>
# JFR (Java Flight Recorder) e VisualVM para profiling
```

```java
// Referências fracas: úteis para caches que não devem impedir o GC
WeakReference<Objeto> ref = new WeakReference<>(new Objeto());
```

### 5.5 Reflection e anotações

Base de frameworks como Spring e Hibernate.

```java
// Anotação customizada
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface NaoVazio {}

public class Cliente {
    @NaoVazio private String nome;
    public Cliente(String nome) { this.nome = nome; }
}

// Processando a anotação com reflection
public static void validar(Object obj) throws IllegalAccessException {
    for (Field campo : obj.getClass().getDeclaredFields()) {
        if (campo.isAnnotationPresent(NaoVazio.class)) {
            campo.setAccessible(true);
            Object valor = campo.get(obj);
            if (valor == null || valor.toString().isBlank()) {
                throw new IllegalStateException("Campo obrigatório: " + campo.getName());
            }
        }
    }
}
```

**Proxies dinâmicos**: o Spring usa esse mecanismo para `@Transactional`, `@Cacheable` e AOP.

```java
Servico original = new ServicoImpl();
Servico proxy = (Servico) Proxy.newProxyInstance(
    Servico.class.getClassLoader(),
    new Class<?>[]{Servico.class},
    (p, metodo, args) -> {
        System.out.println("Antes de " + metodo.getName());
        Object r = metodo.invoke(original, args);
        System.out.println("Depois");
        return r;
    });
```

### 5.6 Design patterns essenciais

```java
// Singleton (via enum: thread-safe e simples)
public enum Config { INSTANCE; public String url = "jdbc:..."; }

// Builder
public class Pedido {
    private final String cliente;
    private final List<String> itens;
    private Pedido(Builder b) { this.cliente = b.cliente; this.itens = b.itens; }

    public static Builder builder() { return new Builder(); }

    public static class Builder {
        private String cliente;
        private List<String> itens = new ArrayList<>();
        public Builder cliente(String c) { this.cliente = c; return this; }
        public Builder item(String i) { this.itens.add(i); return this; }
        public Pedido build() { return new Pedido(this); }
    }
}
Pedido p = Pedido.builder().cliente("Ana").item("Livro").build();

// Strategy com lambdas
interface Desconto { double aplicar(double valor); }
Map<String, Desconto> estrategias = Map.of(
    "NATAL", v -> v * 0.9,
    "VIP",   v -> v * 0.8
);

// Factory
public interface Notificacao { void enviar(String m); }
public class NotificacaoFactory {
    public static Notificacao criar(String tipo) {
        return switch (tipo) {
            case "email" -> m -> System.out.println("Email: " + m);
            case "sms"   -> m -> System.out.println("SMS: " + m);
            default -> throw new IllegalArgumentException(tipo);
        };
    }
}

// Observer, Decorator, Template Method, Adapter...
// Estude também o catálogo GoF e a relação deles com o Spring (Proxy, Template, Factory, Singleton).
```

### 5.7 Testes com Mockito

```java
@ExtendWith(MockitoExtension.class)
class PedidoServiceTest {

    @Mock Notificador notificador;
    @InjectMocks PedidoService service;

    @Test
    void deveNotificarAoFinalizar() {
        service.finalizar();
        verify(notificador, times(1)).enviar("Pedido finalizado");
    }

    @Test
    void exemploDeStub() {
        when(repo.buscar(1L)).thenReturn(Optional.of(new Cliente("Ana")));
        assertEquals("Ana", service.nomeDoCliente(1L));
    }
}
```

### 5.8 Tópicos para aprofundamento

| Tema | O que estudar |
|------|---------------|
| JVM | ClassLoaders, JIT (C1/C2), GraalVM, AOT |
| Concorrência | `ForkJoinPool`, `StampedLock`, Structured Concurrency |
| Performance | JMH (benchmarks), JFR, async-profiler |
| Módulos | JPMS (`module-info.java`) |
| Interoperabilidade | JNI, Foreign Function & Memory API |
| Boas práticas | Effective Java (Joshua Bloch), Clean Code |


---

# PARTE 2: SPRING BOOT

## Módulo 6 · Primeiros passos com Spring Boot

### 6.1 O que é Spring e Spring Boot

- **Spring Framework**: framework com **Inversão de Controle (IoC)** e **Injeção de Dependências (DI)**, além de módulos para web, dados, segurança, mensageria etc.
- **Spring Boot**: camada que elimina configuração manual. Oferece *auto-configuration*, *starters*, servidor embutido (Tomcat) e executável único (`fat jar`).

### 6.2 Criando o projeto

Acesse **https://start.spring.io** e escolha:
- Project: Maven, Language: Java, Spring Boot: 3.x
- Java: 21
- Dependencies: *Spring Web*, *Spring Data JPA*, *Validation*, *H2 Database*, *Lombok* (opcional), *Spring Boot DevTools*

Estrutura gerada:

```
meu-app/
├── pom.xml
└── src/
    ├── main/
    │   ├── java/com/exemplo/meuapp/
    │   │   └── MeuAppApplication.java
    │   └── resources/
    │       ├── application.properties
    │       ├── static/
    │       └── templates/
    └── test/java/com/exemplo/meuapp/
```

### 6.3 Classe principal

```java
@SpringBootApplication   // = @Configuration + @EnableAutoConfiguration + @ComponentScan
public class MeuAppApplication {
    public static void main(String[] args) {
        SpringApplication.run(MeuAppApplication.class, args);
    }
}
```

```bash
./mvnw spring-boot:run
# ou
./mvnw clean package && java -jar target/meu-app-0.0.1-SNAPSHOT.jar
```

### 6.4 Inversão de Controle e Injeção de Dependências

O **container (ApplicationContext)** cria, configura e injeta os objetos (*beans*).

```java
@Component
public class EmailService {
    public void enviar(String para, String msg) {
        System.out.println("Enviando para " + para + ": " + msg);
    }
}

@Service
public class PedidoService {

    private final EmailService emailService;

    // Injeção via construtor (recomendada). Com um único construtor, @Autowired é opcional.
    public PedidoService(EmailService emailService) {
        this.emailService = emailService;
    }

    public void finalizar(String cliente) {
        emailService.enviar(cliente, "Pedido finalizado!");
    }
}
```

**Estereótipos**

| Anotação | Uso |
|----------|-----|
| `@Component` | Bean genérico |
| `@Service` | Regras de negócio |
| `@Repository` | Acesso a dados (traduz exceções de persistência) |
| `@Controller` / `@RestController` | Camada web |
| `@Configuration` + `@Bean` | Definição explícita de beans |

```java
@Configuration
public class AppConfig {

    @Bean
    public Clock clock() {
        return Clock.systemDefaultZone();
    }
}
```

### 6.5 Escopos, qualificadores e ciclo de vida

```java
public interface Notificador { void enviar(String msg); }

@Service("email")
public class EmailNotificador implements Notificador { /* ... */ }

@Service("sms")
public class SmsNotificador implements Notificador { /* ... */ }

@Service
public class Alerta {
    private final Notificador notificador;

    public Alerta(@Qualifier("sms") Notificador notificador) {
        this.notificador = notificador;
    }
}

// @Primary define o candidato padrão quando há ambiguidade
@Primary @Service
public class PushNotificador implements Notificador { /* ... */ }

// Injetar todas as implementações
@Service
public class Dispatcher {
    public Dispatcher(List<Notificador> todos) { /* ... */ }
}
```

```java
@Component
@Scope("prototype")   // singleton (padrão), prototype, request, session
public class Carrinho { }

@Component
public class Recursos {
    @PostConstruct void iniciar() { System.out.println("Bean pronto"); }
    @PreDestroy   void encerrar() { System.out.println("Bean será destruído"); }
}
```

### 6.6 Propriedades e `application.properties`/`application.yml`

```yaml
# application.yml
server:
  port: 8080

spring:
  application:
    name: meu-app

app:
  mensagem: Bem-vindo!
  limite-requisicoes: 100
```

```java
@Value("${app.mensagem}")
private String mensagem;

// Preferível para grupos de propriedades: tipado e validável
@ConfigurationProperties(prefix = "app")
public record AppProperties(String mensagem, int limiteRequisicoes) {}

@SpringBootApplication
@ConfigurationPropertiesScan
public class MeuAppApplication { /* ... */ }
```

---

## Módulo 7 · API REST

### 7.1 Primeiro controller

```java
@RestController
@RequestMapping("/api/ola")
public class OlaController {

    @GetMapping
    public String ola() {
        return "Olá, Spring Boot!";
    }

    @GetMapping("/{nome}")
    public Map<String, String> saudar(@PathVariable String nome,
                                       @RequestParam(defaultValue = "pt") String idioma) {
        return Map.of("mensagem", "Olá, " + nome, "idioma", idioma);
    }
}
```

### 7.2 CRUD completo

**DTOs** (separe o contrato da API do modelo de domínio):

```java
public record ProdutoRequest(
        @NotBlank(message = "Nome é obrigatório")
        @Size(max = 100)
        String nome,

        @NotNull
        @Positive(message = "Preço deve ser positivo")
        BigDecimal preco,

        @Min(0)
        Integer estoque
) {}

public record ProdutoResponse(Long id, String nome, BigDecimal preco, Integer estoque) {}
```

**Controller**:

```java
@RestController
@RequestMapping("/api/produtos")
public class ProdutoController {

    private final ProdutoService service;

    public ProdutoController(ProdutoService service) { this.service = service; }

    @GetMapping
    public List<ProdutoResponse> listar() {
        return service.listar();
    }

    @GetMapping("/{id}")
    public ProdutoResponse buscar(@PathVariable Long id) {
        return service.buscar(id);
    }

    @PostMapping
    public ResponseEntity<ProdutoResponse> criar(@Valid @RequestBody ProdutoRequest req,
                                                  UriComponentsBuilder uri) {
        ProdutoResponse criado = service.criar(req);
        URI location = uri.path("/api/produtos/{id}").buildAndExpand(criado.id()).toUri();
        return ResponseEntity.created(location).body(criado);   // 201 + header Location
    }

    @PutMapping("/{id}")
    public ProdutoResponse atualizar(@PathVariable Long id, @Valid @RequestBody ProdutoRequest req) {
        return service.atualizar(id, req);
    }

    @DeleteMapping("/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void remover(@PathVariable Long id) {
        service.remover(id);
    }
}
```

**Convenções REST**

| Verbo | Rota | Ação | Status de sucesso |
|-------|------|------|-------------------|
| GET | `/produtos` | Listar | 200 |
| GET | `/produtos/{id}` | Buscar | 200 (404 se não existe) |
| POST | `/produtos` | Criar | 201 |
| PUT | `/produtos/{id}` | Substituir | 200 |
| PATCH | `/produtos/{id}` | Atualização parcial | 200 |
| DELETE | `/produtos/{id}` | Remover | 204 |

### 7.3 Tratamento global de erros

```java
public class RecursoNaoEncontradoException extends RuntimeException {
    public RecursoNaoEncontradoException(String msg) { super(msg); }
}

@RestControllerAdvice
public class GlobalExceptionHandler {

    // Padrão RFC 7807 (ProblemDetail), nativo no Spring 6
    @ExceptionHandler(RecursoNaoEncontradoException.class)
    public ProblemDetail naoEncontrado(RecursoNaoEncontradoException ex) {
        ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        pd.setTitle("Recurso não encontrado");
        return pd;
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ProblemDetail validacao(MethodArgumentNotValidException ex) {
        ProblemDetail pd = ProblemDetail.forStatus(HttpStatus.BAD_REQUEST);
        pd.setTitle("Dados inválidos");
        Map<String, String> erros = new HashMap<>();
        ex.getBindingResult().getFieldErrors()
          .forEach(e -> erros.put(e.getField(), e.getDefaultMessage()));
        pd.setProperty("erros", erros);
        return pd;
    }

    @ExceptionHandler(Exception.class)
    public ProblemDetail generico(Exception ex) {
        // Registre o erro em log; nunca exponha detalhes internos ao cliente
        return ProblemDetail.forStatusAndDetail(HttpStatus.INTERNAL_SERVER_ERROR, "Erro interno");
    }
}
```

### 7.4 Validação customizada

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = CpfValidator.class)
public @interface Cpf {
    String message() default "CPF inválido";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

public class CpfValidator implements ConstraintValidator<Cpf, String> {
    @Override
    public boolean isValid(String valor, ConstraintValidatorContext ctx) {
        return valor == null || valor.matches("\\d{11}");  // simplificado
    }
}
```

### 7.5 Mapeamento entre entidade e DTO

```java
// Manualmente (simples e explícito)
public static ProdutoResponse toResponse(Produto p) {
    return new ProdutoResponse(p.getId(), p.getNome(), p.getPreco(), p.getEstoque());
}
```

Para projetos maiores, use **MapStruct** (gera o código em tempo de compilação):

```java
@Mapper(componentModel = "spring")
public interface ProdutoMapper {
    ProdutoResponse toResponse(Produto produto);
    Produto toEntity(ProdutoRequest request);
}
```

---

## Módulo 8 · Persistência com Spring Data JPA

### 8.1 Dependências e configuração

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
  <groupId>org.postgresql</groupId>
  <artifactId>postgresql</artifactId>
  <scope>runtime</scope>
</dependency>
```

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/meubanco
    username: postgres
    password: ${DB_PASSWORD}
  jpa:
    hibernate:
      ddl-auto: validate      # em produção: use migrations, não "update"
    open-in-view: false       # evita lazy loading acidental na camada web
    show-sql: false
    properties:
      hibernate.format_sql: true
```

### 8.2 Entidades

```java
@Entity
@Table(name = "produtos")
public class Produto {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String nome;

    @Column(nullable = false, precision = 10, scale = 2)
    private BigDecimal preco;

    private Integer estoque;

    @Enumerated(EnumType.STRING)       // nunca use ORDINAL
    private StatusProduto status = StatusProduto.ATIVO;

    @CreationTimestamp
    private LocalDateTime criadoEm;

    @Version                            // controle de concorrência otimista
    private Long versao;

    protected Produto() {}              // exigido pelo JPA

    public Produto(String nome, BigDecimal preco, Integer estoque) {
        this.nome = nome;
        this.preco = preco;
        this.estoque = estoque;
    }

    // getters e setters...
}
```

### 8.3 Repositórios

```java
public interface ProdutoRepository extends JpaRepository<Produto, Long> {

    // Query derivada pelo nome do método
    List<Produto> findByNomeContainingIgnoreCase(String nome);
    List<Produto> findByPrecoBetweenOrderByPrecoAsc(BigDecimal min, BigDecimal max);
    boolean existsByNome(String nome);
    Optional<Produto> findFirstByStatus(StatusProduto status);

    // JPQL
    @Query("select p from Produto p where p.estoque < :limite and p.status = 'ATIVO'")
    List<Produto> estoqueBaixo(@Param("limite") int limite);

    // Query nativa
    @Query(value = "select * from produtos where preco > ?1", nativeQuery = true)
    List<Produto> caros(BigDecimal minimo);

    // Atualização em lote
    @Modifying
    @Transactional
    @Query("update Produto p set p.preco = p.preco * :fator where p.status = 'ATIVO'")
    int reajustar(@Param("fator") BigDecimal fator);

    // Projeção (traz apenas o necessário)
    interface ResumoProduto { Long getId(); String getNome(); }
    List<ResumoProduto> findByStatus(StatusProduto status);
}
```

### 8.4 Paginação e ordenação

```java
@GetMapping
public Page<ProdutoResponse> listar(
        @RequestParam(defaultValue = "0") int pagina,
        @RequestParam(defaultValue = "10") int tamanho,
        @RequestParam(defaultValue = "nome") String ordenarPor) {

    Pageable pageable = PageRequest.of(pagina, tamanho, Sort.by(ordenarPor).ascending());
    return repository.findAll(pageable).map(ProdutoMapper::toResponse);
}
```

Ou deixe o Spring resolver direto: `listar(Pageable pageable)` e chame `GET /produtos?page=0&size=10&sort=nome,asc`.

### 8.5 Camada de serviço e transações

```java
@Service
public class ProdutoService {

    private final ProdutoRepository repository;

    public ProdutoService(ProdutoRepository repository) { this.repository = repository; }

    @Transactional(readOnly = true)
    public ProdutoResponse buscar(Long id) {
        return repository.findById(id)
                .map(ProdutoMapper::toResponse)
                .orElseThrow(() -> new RecursoNaoEncontradoException("Produto " + id + " não encontrado"));
    }

    @Transactional
    public ProdutoResponse criar(ProdutoRequest req) {
        if (repository.existsByNome(req.nome())) {
            throw new IllegalStateException("Produto já existe");
        }
        Produto salvo = repository.save(new Produto(req.nome(), req.preco(), req.estoque()));
        return ProdutoMapper.toResponse(salvo);
    }

    @Transactional
    public ProdutoResponse atualizar(Long id, ProdutoRequest req) {
        Produto p = repository.findById(id)
                .orElseThrow(() -> new RecursoNaoEncontradoException("Produto " + id));
        p.setNome(req.nome());
        p.setPreco(req.preco());
        // Sem chamar save(): a entidade é gerenciada e o dirty checking grava ao final da transação
        return ProdutoMapper.toResponse(p);
    }
}
```

**Propagação e isolamento**

```java
@Transactional(propagation = Propagation.REQUIRES_NEW)   // abre transação independente
@Transactional(rollbackFor = Exception.class)             // por padrão, só RuntimeException reverte
@Transactional(isolation = Isolation.SERIALIZABLE)
```

> **Armadilha clássica**: `@Transactional` funciona via proxy. Chamar um método anotado a partir de outro método **da mesma classe** (`this.metodo()`) **não** passa pelo proxy, e a anotação é ignorada.

### 8.6 Relacionamentos

```java
@Entity
public class Categoria {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String nome;

    @OneToMany(mappedBy = "categoria", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Produto> produtos = new ArrayList<>();
}

@Entity
public class Produto {
    // ...
    @ManyToOne(fetch = FetchType.LAZY, optional = false)   // sempre LAZY em *ToOne
    @JoinColumn(name = "categoria_id")
    private Categoria categoria;

    @ManyToMany
    @JoinTable(name = "produto_tag",
        joinColumns = @JoinColumn(name = "produto_id"),
        inverseJoinColumns = @JoinColumn(name = "tag_id"))
    private Set<Tag> tags = new HashSet<>();
}
```

### 8.7 O problema N+1 e como resolver

Ao listar 100 produtos e acessar `produto.getCategoria().getNome()` (lazy), são feitas 1 + 100 consultas.

```java
// Solução 1: JOIN FETCH
@Query("select p from Produto p join fetch p.categoria")
List<Produto> listarComCategoria();

// Solução 2: EntityGraph
@EntityGraph(attributePaths = {"categoria", "tags"})
List<Produto> findByStatus(StatusProduto status);

// Solução 3: Batch fetching (application.yml)
// spring.jpa.properties.hibernate.default_batch_fetch_size: 50

// Solução 4: projeção/DTO direto na query
@Query("select new com.exemplo.dto.ProdutoResumo(p.id, p.nome, c.nome) " +
       "from Produto p join p.categoria c")
List<ProdutoResumo> resumo();
```

### 8.8 Migrations com Flyway

```xml
<dependency>
  <groupId>org.flywaydb</groupId>
  <artifactId>flyway-core</artifactId>
</dependency>
<dependency>
  <groupId>org.flywaydb</groupId>
  <artifactId>flyway-database-postgresql</artifactId>
</dependency>
```

`src/main/resources/db/migration/V1__criar_tabela_produtos.sql`:

```sql
CREATE TABLE produtos (
    id         BIGSERIAL PRIMARY KEY,
    nome       VARCHAR(100)   NOT NULL,
    preco      NUMERIC(10, 2) NOT NULL,
    estoque    INTEGER,
    status     VARCHAR(20)    NOT NULL DEFAULT 'ATIVO',
    criado_em  TIMESTAMP      NOT NULL DEFAULT now(),
    versao     BIGINT         NOT NULL DEFAULT 0
);

CREATE INDEX idx_produtos_nome ON produtos (nome);
```

### 8.9 Specifications (filtros dinâmicos)

```java
public interface ProdutoRepository
        extends JpaRepository<Produto, Long>, JpaSpecificationExecutor<Produto> {}

public class ProdutoSpecs {
    public static Specification<Produto> nomeContem(String nome) {
        return (root, q, cb) -> nome == null ? null
                : cb.like(cb.lower(root.get("nome")), "%" + nome.toLowerCase() + "%");
    }
    public static Specification<Produto> precoMaximo(BigDecimal max) {
        return (root, q, cb) -> max == null ? null : cb.lessThanOrEqualTo(root.get("preco"), max);
    }
}

repository.findAll(where(nomeContem("mouse")).and(precoMaximo(BigDecimal.valueOf(100))), pageable);
```

### 8.10 Auditoria e Spring Data JDBC/MongoDB (visão geral)

```java
@Configuration
@EnableJpaAuditing
public class JpaConfig {}

@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class Auditavel {
    @CreatedDate     private Instant criadoEm;
    @LastModifiedDate private Instant atualizadoEm;
    @CreatedBy       private String criadoPor;
}
```

O Spring Data também oferece módulos para **MongoDB**, **Redis**, **Elasticsearch**, **Cassandra** e **JDBC**, todos com o mesmo estilo de repositório.

---

## Módulo 9 · Configuração, profiles e arquitetura

### 9.1 Profiles

```yaml
# application.yml
spring:
  profiles:
    active: dev

---
spring:
  config:
    activate:
      on-profile: dev
  datasource:
    url: jdbc:h2:mem:devdb

---
spring:
  config:
    activate:
      on-profile: prod
  datasource:
    url: ${DATABASE_URL}
```

```java
@Configuration
@Profile("dev")
public class DevConfig {
    @Bean CommandLineRunner seed(ProdutoRepository repo) {
        return args -> repo.save(new Produto("Exemplo", BigDecimal.TEN, 5));
    }
}
```

```bash
java -jar app.jar --spring.profiles.active=prod
SPRING_PROFILES_ACTIVE=prod java -jar app.jar
```

### 9.2 Arquitetura em camadas

```
com.exemplo.loja
├── produto
│   ├── ProdutoController.java     ← web (HTTP ↔ DTO)
│   ├── ProdutoService.java        ← regras de negócio e transações
│   ├── ProdutoRepository.java     ← acesso a dados
│   ├── Produto.java               ← entidade
│   └── dto/
├── pedido
├── shared
│   ├── exception/
│   └── config/
└── LojaApplication.java
```

Prefira **organizar por funcionalidade (feature/domínio)** em vez de por tipo técnico (`controllers/`, `services/`). Facilita a evolução para módulos e microsserviços.

**Arquitetura hexagonal (Ports & Adapters)**: o domínio não depende de frameworks.

```java
// domínio: porta (interface)
public interface PedidoRepositoryPort {
    Pedido salvar(Pedido p);
}

// infraestrutura: adaptador
@Repository
class PedidoJpaAdapter implements PedidoRepositoryPort {
    private final PedidoJpaRepository jpa;
    PedidoJpaAdapter(PedidoJpaRepository jpa) { this.jpa = jpa; }
    public Pedido salvar(Pedido p) { /* converte e persiste */ return p; }
}
```

### 9.3 Logging

```java
@Service
public class PedidoService {
    private static final Logger log = LoggerFactory.getLogger(PedidoService.class);

    public void processar(Long id) {
        log.info("Processando pedido {}", id);       // use placeholders, não concatenação
        try { /* ... */ }
        catch (Exception e) { log.error("Falha ao processar pedido {}", id, e); }
    }
}
```

```yaml
logging:
  level:
    root: INFO
    com.exemplo: DEBUG
    org.hibernate.SQL: DEBUG
```

### 9.4 CORS

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("https://meu-front.com")
                .allowedMethods("GET", "POST", "PUT", "DELETE")
                .allowCredentials(true);
    }
}
```

### 9.5 Interceptors e Filters

```java
@Component
public class TempoRequisicaoInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest req, HttpServletResponse res, Object h) {
        req.setAttribute("inicio", System.currentTimeMillis());
        return true;
    }

    @Override
    public void afterCompletion(HttpServletRequest req, HttpServletResponse res, Object h, Exception ex) {
        long ms = System.currentTimeMillis() - (long) req.getAttribute("inicio");
        System.out.println(req.getRequestURI() + " levou " + ms + "ms");
    }
}

@Configuration
class WebMvcConfig implements WebMvcConfigurer {
    private final TempoRequisicaoInterceptor interceptor;
    WebMvcConfig(TempoRequisicaoInterceptor i) { this.interceptor = i; }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(interceptor);
    }
}
```

---

## Módulo 10 · Spring Security e JWT

### 10.1 Conceitos

- **Autenticação**: quem é você? **Autorização**: o que você pode fazer?
- O Spring Security é uma **cadeia de filtros** (`SecurityFilterChain`) antes dos controllers.
- Em APIs REST modernas, use autenticação **stateless** com **JWT** ou **OAuth2/OIDC**.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 10.2 Configuração básica

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity   // habilita @PreAuthorize
public class SecurityConfig {

    @Bean
    SecurityFilterChain filterChain(HttpSecurity http, JwtAuthFilter jwtFilter) throws Exception {
        return http
            .csrf(csrf -> csrf.disable())    // aceitável em API stateless com token no header
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**", "/v3/api-docs/**", "/swagger-ui/**").permitAll()
                .requestMatchers(HttpMethod.GET, "/api/produtos/**").hasAnyRole("USER", "ADMIN")
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated())
            .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class)
            .build();
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    AuthenticationManager authenticationManager(AuthenticationConfiguration cfg) throws Exception {
        return cfg.getAuthenticationManager();
    }
}
```

### 10.3 Usuários vindos do banco

```java
@Entity
public class Usuario {
    @Id @GeneratedValue private Long id;
    @Column(unique = true) private String email;
    private String senhaHash;
    @Enumerated(EnumType.STRING) private Papel papel;   // USER, ADMIN
    // getters...
}

@Service
public class UsuarioDetailsService implements UserDetailsService {
    private final UsuarioRepository repo;
    public UsuarioDetailsService(UsuarioRepository repo) { this.repo = repo; }

    @Override
    public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
        Usuario u = repo.findByEmail(email)
                .orElseThrow(() -> new UsernameNotFoundException("Usuário não encontrado"));
        return User.withUsername(u.getEmail())
                .password(u.getSenhaHash())
                .roles(u.getPapel().name())
                .build();
    }
}
```

### 10.4 Geração e validação de JWT

Dependência: `io.jsonwebtoken:jjwt-api`, `jjwt-impl`, `jjwt-jackson`.

```java
@Service
public class JwtService {

    private final SecretKey chave;
    private final long expiracaoMs;

    public JwtService(@Value("${app.jwt.secret}") String segredo,
                      @Value("${app.jwt.expiracao-ms}") long expiracaoMs) {
        this.chave = Keys.hmacShaKeyFor(Decoders.BASE64.decode(segredo));
        this.expiracaoMs = expiracaoMs;
    }

    public String gerar(UserDetails user) {
        return Jwts.builder()
                .subject(user.getUsername())
                .claim("roles", user.getAuthorities().stream().map(GrantedAuthority::getAuthority).toList())
                .issuedAt(new Date())
                .expiration(new Date(System.currentTimeMillis() + expiracaoMs))
                .signWith(chave)
                .compact();
    }

    public String extrairUsuario(String token) {
        return Jwts.parser().verifyWith(chave).build()
                .parseSignedClaims(token).getPayload().getSubject();
    }
}

@Component
public class JwtAuthFilter extends OncePerRequestFilter {

    private final JwtService jwt;
    private final UserDetailsService users;

    public JwtAuthFilter(JwtService jwt, UserDetailsService users) {
        this.jwt = jwt; this.users = users;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain chain)
            throws ServletException, IOException {

        String header = req.getHeader("Authorization");
        if (header != null && header.startsWith("Bearer ")) {
            try {
                String email = jwt.extrairUsuario(header.substring(7));
                if (SecurityContextHolder.getContext().getAuthentication() == null) {
                    UserDetails ud = users.loadUserByUsername(email);
                    var auth = new UsernamePasswordAuthenticationToken(ud, null, ud.getAuthorities());
                    SecurityContextHolder.getContext().setAuthentication(auth);
                }
            } catch (JwtException | UsernameNotFoundException e) {
                // token inválido: segue sem autenticar; o Spring responderá 401/403
            }
        }
        chain.doFilter(req, res);
    }
}

@RestController
@RequestMapping("/api/auth")
public class AuthController {
    private final AuthenticationManager manager;
    private final JwtService jwt;
    private final UserDetailsService users;
    // construtor...

    @PostMapping("/login")
    public Map<String, String> login(@Valid @RequestBody LoginRequest req) {
        manager.authenticate(new UsernamePasswordAuthenticationToken(req.email(), req.senha()));
        String token = jwt.gerar(users.loadUserByUsername(req.email()));
        return Map.of("token", token);
    }
}
```

### 10.5 Autorização por método

```java
@PreAuthorize("hasRole('ADMIN')")
@DeleteMapping("/{id}")
public void remover(@PathVariable Long id) { /* ... */ }

@PreAuthorize("#email == authentication.name or hasRole('ADMIN')")
public Usuario buscarPorEmail(String email) { /* ... */ }
```

### 10.6 Boas práticas de segurança

- Nunca armazene senhas em texto puro: use **BCrypt/Argon2**.
- Guarde segredos em variáveis de ambiente ou *secret managers* (Vault, AWS Secrets Manager).
- Use HTTPS, tokens de curta duração e *refresh tokens*.
- Valide toda entrada. Use consultas parametrizadas (o JPA já faz isso).
- Não retorne entidades diretamente (vazamento de campos como `senhaHash`).
- Considere **OAuth2 Resource Server** (`spring-boot-starter-oauth2-resource-server`) com Keycloak/Auth0/Cognito em vez de implementar JWT manualmente.
- Revise o **OWASP Top 10** e mantenha as dependências atualizadas.

---

## Módulo 11 · Testes

### 11.1 Pirâmide de testes

```
        /\        E2E (poucos)
       /──\       Integração (alguns)
      /────\      Unitários (muitos, rápidos)
```

### 11.2 Teste unitário de serviço

```java
@ExtendWith(MockitoExtension.class)
class ProdutoServiceTest {

    @Mock ProdutoRepository repository;
    @InjectMocks ProdutoService service;

    @Test
    void deveLancarExcecaoQuandoProdutoNaoExiste() {
        when(repository.findById(99L)).thenReturn(Optional.empty());

        assertThrows(RecursoNaoEncontradoException.class, () -> service.buscar(99L));
    }

    @Test
    void deveCriarProduto() {
        var req = new ProdutoRequest("Mouse", BigDecimal.valueOf(50), 10);
        when(repository.existsByNome("Mouse")).thenReturn(false);
        when(repository.save(any(Produto.class))).thenAnswer(i -> i.getArgument(0));

        var resp = service.criar(req);

        assertEquals("Mouse", resp.nome());
        verify(repository).save(any(Produto.class));
    }
}
```

### 11.3 Teste da camada web (`@WebMvcTest`)

```java
@WebMvcTest(ProdutoController.class)
class ProdutoControllerTest {

    @Autowired MockMvc mockMvc;
    @Autowired ObjectMapper mapper;
    @MockitoBean ProdutoService service;   // Spring Boot 3.4+ (antes: @MockBean)

    @Test
    void deveRetornar400QuandoNomeVazio() throws Exception {
        var req = new ProdutoRequest("", BigDecimal.TEN, 1);

        mockMvc.perform(post("/api/produtos")
                .contentType(MediaType.APPLICATION_JSON)
                .content(mapper.writeValueAsString(req)))
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.erros.nome").exists());
    }

    @Test
    void deveBuscarProduto() throws Exception {
        when(service.buscar(1L)).thenReturn(new ProdutoResponse(1L, "Mouse", BigDecimal.TEN, 5));

        mockMvc.perform(get("/api/produtos/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.nome").value("Mouse"));
    }
}
```

### 11.4 Teste de repositório (`@DataJpaTest`)

```java
@DataJpaTest
class ProdutoRepositoryTest {

    @Autowired ProdutoRepository repository;

    @Test
    void deveBuscarPorNome() {
        repository.save(new Produto("Teclado Gamer", BigDecimal.valueOf(200), 3));

        var resultado = repository.findByNomeContainingIgnoreCase("teclado");

        assertEquals(1, resultado.size());
    }
}
```

### 11.5 Teste de integração com Testcontainers

Use um banco **real** (PostgreSQL em container) em vez de H2, para evitar divergências de comportamento.

```java
@SpringBootTest
@Testcontainers
class ProdutoIntegrationTest {

    @Container
    @ServiceConnection   // Spring Boot 3.1+: configura o datasource automaticamente
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:16-alpine");

    @Autowired ProdutoService service;

    @Test
    void devePersistirEBuscar() {
        var criado = service.criar(new ProdutoRequest("Monitor", BigDecimal.valueOf(900), 2));
        assertNotNull(service.buscar(criado.id()));
    }
}
```

### 11.6 Testando endpoints protegidos

```java
@Test
@WithMockUser(roles = "ADMIN")
void adminPodeRemover() throws Exception {
    mockMvc.perform(delete("/api/produtos/1")).andExpect(status().isNoContent());
}

@Test
void anonimoRecebe401() throws Exception {
    mockMvc.perform(get("/api/produtos")).andExpect(status().isUnauthorized());
}
```

---

## Módulo 12 · Spring Boot avançado

### 12.1 Cache

```java
@Configuration
@EnableCaching
public class CacheConfig {}

@Service
public class CategoriaService {

    @Cacheable(value = "categorias", key = "#id")
    public Categoria buscar(Long id) {
        // só executa em caso de cache miss
        return repo.findById(id).orElseThrow();
    }

    @CachePut(value = "categorias", key = "#result.id")
    public Categoria atualizar(Categoria c) { return repo.save(c); }

    @CacheEvict(value = "categorias", key = "#id")
    public void remover(Long id) { repo.deleteById(id); }
}
```

Em produção, use **Redis** (`spring-boot-starter-data-redis`) ou **Caffeine** (cache local com TTL):

```yaml
spring:
  cache:
    type: caffeine
    caffeine:
      spec: maximumSize=500,expireAfterWrite=10m
```

### 12.2 Processamento assíncrono e agendamento

```java
@Configuration
@EnableAsync
@EnableScheduling
public class AsyncConfig {}

@Service
public class RelatorioService {

    @Async
    public CompletableFuture<String> gerar(Long id) {
        // executa em outra thread
        return CompletableFuture.completedFuture("Relatório " + id);
    }

    @Scheduled(cron = "0 0 2 * * *")       // todo dia às 02:00
    public void limpezaNoturna() { /* ... */ }

    @Scheduled(fixedRate = 60_000)         // a cada minuto
    public void healthPing() { /* ... */ }
}
```

Em instâncias múltiplas, use **ShedLock** para evitar que o job rode em todas simultaneamente.

**Virtual Threads no Spring Boot 3.2+**:

```yaml
spring:
  threads:
    virtual:
      enabled: true
```

### 12.3 Eventos de aplicação

Desacopla módulos dentro do mesmo processo.

```java
public record PedidoCriadoEvent(Long pedidoId, String email) {}

@Service
public class PedidoService {
    private final ApplicationEventPublisher publisher;
    // construtor...

    @Transactional
    public void criar(...) {
        // salva o pedido
        publisher.publishEvent(new PedidoCriadoEvent(pedido.getId(), cliente.getEmail()));
    }
}

@Component
public class PedidoListener {

    // Só dispara se a transação der commit
    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    @Async
    public void aoCriar(PedidoCriadoEvent e) {
        System.out.println("Enviar e-mail para " + e.email());
    }
}
```

### 12.4 AOP: Programação Orientada a Aspectos

```java
@Aspect
@Component
public class LogTempoAspect {

    @Around("@annotation(com.exemplo.aop.Medir)")
    public Object medir(ProceedingJoinPoint pjp) throws Throwable {
        long ini = System.nanoTime();
        try {
            return pjp.proceed();
        } finally {
            long ms = (System.nanoTime() - ini) / 1_000_000;
            System.out.println(pjp.getSignature() + " levou " + ms + "ms");
        }
    }
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface Medir {}

@Medir
public void operacaoLenta() { /* ... */ }
```

### 12.5 Clientes HTTP: `RestClient` e HTTP Interfaces

```java
@Configuration
public class ClientConfig {

    @Bean
    RestClient viaCepClient(RestClient.Builder builder) {
        return builder.baseUrl("https://viacep.com.br/ws").build();
    }
}

@Service
public class CepService {
    private final RestClient client;
    public CepService(RestClient viaCepClient) { this.client = viaCepClient; }

    public Endereco buscar(String cep) {
        return client.get()
                .uri("/{cep}/json", cep)
                .retrieve()
                .onStatus(HttpStatusCode::is4xxClientError, (req, res) -> {
                    throw new IllegalArgumentException("CEP inválido");
                })
                .body(Endereco.class);
    }
}

// Interface declarativa (Spring 6)
@HttpExchange("/ws")
public interface CepClient {
    @GetExchange("/{cep}/json")
    Endereco buscar(@PathVariable String cep);
}
```

Para fluxos reativos e não bloqueantes, existe o **Spring WebFlux** com `WebClient`, `Mono` e `Flux`:

```java
@GetMapping("/stream")
public Flux<Produto> stream() {
    return repositorioReativo.findAll().delayElements(Duration.ofMillis(200));
}
```

### 12.5.1 Resiliência com Resilience4j

```java
@Service
public class PagamentoClient {

    @CircuitBreaker(name = "pagamento", fallbackMethod = "fallback")
    @Retry(name = "pagamento")
    @TimeLimiter(name = "pagamento")
    public String cobrar(Long pedidoId) {
        return restClient.post().uri("/cobrar/{id}", pedidoId).retrieve().body(String.class);
    }

    private String fallback(Long pedidoId, Throwable t) {
        return "Pagamento pendente (serviço indisponível)";
    }
}
```

```yaml
resilience4j:
  circuitbreaker:
    instances:
      pagamento:
        failure-rate-threshold: 50
        sliding-window-size: 10
        wait-duration-in-open-state: 30s
  retry:
    instances:
      pagamento:
        max-attempts: 3
        wait-duration: 500ms
```

### 12.6 Documentação OpenAPI/Swagger

```xml
<dependency>
  <groupId>org.springdoc</groupId>
  <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
  <version>2.5.0</version>
</dependency>
```

```java
@Operation(summary = "Busca um produto", description = "Retorna o produto pelo ID")
@ApiResponses({
    @ApiResponse(responseCode = "200", description = "Encontrado"),
    @ApiResponse(responseCode = "404", description = "Não encontrado")
})
@GetMapping("/{id}")
public ProdutoResponse buscar(@PathVariable Long id) { /* ... */ }
```

Interface disponível em `http://localhost:8080/swagger-ui.html`.

### 12.7 Mensageria: Kafka e RabbitMQ

**Kafka**

```java
@Service
public class PedidoProducer {
    private final KafkaTemplate<String, PedidoCriadoEvent> kafka;
    public PedidoProducer(KafkaTemplate<String, PedidoCriadoEvent> kafka) { this.kafka = kafka; }

    public void publicar(PedidoCriadoEvent e) {
        kafka.send("pedidos.criados", String.valueOf(e.pedidoId()), e);
    }
}

@Component
public class PedidoConsumer {

    @KafkaListener(topics = "pedidos.criados", groupId = "notificacoes")
    public void consumir(PedidoCriadoEvent e) {
        System.out.println("Recebido: " + e);
    }
}
```

**RabbitMQ**

```java
@RabbitListener(queues = "fila.pedidos")
public void receber(PedidoCriadoEvent e) { /* ... */ }

rabbitTemplate.convertAndSend("exchange.pedidos", "pedido.criado", evento);
```

**Conceitos-chave**: idempotência do consumidor, *dead letter queue*, *retry with backoff*, ordenação por chave de partição, e o padrão **Transactional Outbox** para garantir consistência entre banco e mensageria.

### 12.8 Spring Batch (processamento em lote)

```java
@Configuration
public class ImportacaoJobConfig {

    @Bean
    Job importarProdutos(JobRepository repo, Step passo) {
        return new JobBuilder("importarProdutos", repo).start(passo).build();
    }

    @Bean
    Step passo(JobRepository repo, PlatformTransactionManager tx,
               FlatFileItemReader<ProdutoCsv> reader,
               ItemProcessor<ProdutoCsv, Produto> processor,
               JpaItemWriter<Produto> writer) {
        return new StepBuilder("passo", repo)
                .<ProdutoCsv, Produto>chunk(500, tx)     // lê, processa e grava de 500 em 500
                .reader(reader).processor(processor).writer(writer)
                .build();
    }
}
```

### 12.9 Auto-configuração customizada e starters

```java
@AutoConfiguration
@ConditionalOnClass(MeuCliente.class)
@ConditionalOnProperty(prefix = "meu.cliente", name = "enabled", havingValue = "true", matchIfMissing = true)
@EnableConfigurationProperties(MeuClienteProperties.class)
public class MeuClienteAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public MeuCliente meuCliente(MeuClienteProperties props) {
        return new MeuCliente(props.getUrl());
    }
}
```

Registre em `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`:

```
com.exemplo.autoconfig.MeuClienteAutoConfiguration
```

Use `--debug` na inicialização para ver o *conditions report* e entender o que foi (ou não) auto-configurado.

### 12.10 Upload e download de arquivos

```java
@PostMapping(value = "/upload", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
public String upload(@RequestParam("arquivo") MultipartFile arquivo) throws IOException {
    Path destino = Path.of("uploads").resolve(UUID.randomUUID() + "-" + 
                    StringUtils.cleanPath(Objects.requireNonNull(arquivo.getOriginalFilename())));
    Files.createDirectories(destino.getParent());
    arquivo.transferTo(destino);
    return destino.getFileName().toString();
}

@GetMapping("/download/{nome}")
public ResponseEntity<Resource> download(@PathVariable String nome) throws IOException {
    Path caminho = Path.of("uploads").resolve(nome).normalize();   // evita path traversal
    Resource recurso = new UrlResource(caminho.toUri());
    return ResponseEntity.ok()
            .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + nome + "\"")
            .body(recurso);
}
```

### 12.11 Server-Sent Events e WebSocket

```java
@GetMapping(value = "/eventos", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
public SseEmitter eventos() {
    SseEmitter emitter = new SseEmitter(0L);
    executor.submit(() -> {
        try {
            for (int i = 0; i < 5; i++) {
                emitter.send("evento " + i);
                Thread.sleep(1000);
            }
            emitter.complete();
        } catch (Exception e) { emitter.completeWithError(e); }
    });
    return emitter;
}
```

---

## Módulo 13 · Microsserviços e produção

### 13.1 Actuator: observabilidade

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      show-details: when_authorized
      probes:
        enabled: true      # /actuator/health/liveness e /readiness para Kubernetes
```

```java
// Health indicator customizado
@Component
public class GatewayHealthIndicator implements HealthIndicator {
    @Override
    public Health health() {
        boolean ok = verificarGateway();
        return ok ? Health.up().build() : Health.down().withDetail("motivo", "timeout").build();
    }
}

// Métrica customizada
@Service
public class PedidoMetrics {
    private final Counter pedidos;
    public PedidoMetrics(MeterRegistry registry) {
        this.pedidos = Counter.builder("pedidos.criados").tag("origem", "api").register(registry);
    }
    public void registrar() { pedidos.increment(); }
}
```

**Os três pilares da observabilidade**: logs, métricas (Prometheus + Grafana) e *traces* distribuídos (Micrometer Tracing + OpenTelemetry + Zipkin/Jaeger).

```yaml
management:
  tracing:
    sampling:
      probability: 1.0
logging:
  pattern:
    level: "%5p [${spring.application.name:},%X{traceId:-},%X{spanId:-}]"
```

### 13.2 Docker

**Dockerfile multi-stage**:

```dockerfile
FROM eclipse-temurin:21-jdk AS build
WORKDIR /app
COPY .mvn/ .mvn
COPY mvnw pom.xml ./
RUN ./mvnw dependency:go-offline
COPY src ./src
RUN ./mvnw clean package -DskipTests

FROM eclipse-temurin:21-jre
WORKDIR /app
RUN useradd -r appuser
USER appuser
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-XX:MaxRAMPercentage=75", "-jar", "app.jar"]
```

Ou, sem Dockerfile, com Buildpacks:

```bash
./mvnw spring-boot:build-image
```

**docker-compose.yml**:

```yaml
services:
  app:
    build: .
    ports: ["8080:8080"]
    environment:
      SPRING_PROFILES_ACTIVE: prod
      DATABASE_URL: jdbc:postgresql://db:5432/loja
      DB_PASSWORD: segredo
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: loja
      POSTGRES_PASSWORD: segredo
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      retries: 5
```

> O Spring Boot 3.1+ também integra com **Docker Compose** em desenvolvimento (`spring-boot-docker-compose`): ao rodar a aplicação, ele sobe os serviços do `compose.yaml` automaticamente.

### 13.3 Arquitetura de microsserviços

```
                ┌──────────────┐
  Cliente ────► │ API Gateway  │ (Spring Cloud Gateway)
                └──────┬───────┘
         ┌─────────────┼─────────────┐
         ▼             ▼             ▼
   ┌──────────┐  ┌──────────┐  ┌──────────┐
   │ Produtos │  │ Pedidos  │  │ Pagamento│    cada serviço tem seu banco
   └────┬─────┘  └────┬─────┘  └────┬─────┘
        └─────────── Kafka / RabbitMQ ───────┘
```

**Spring Cloud**: peças comuns

| Necessidade | Solução |
|-------------|---------|
| Roteamento e filtros | Spring Cloud Gateway |
| Configuração centralizada | Spring Cloud Config, ou ConfigMaps/Secrets do Kubernetes |
| Descoberta de serviços | Eureka, Consul, ou o DNS do Kubernetes |
| Chamadas entre serviços | OpenFeign, `RestClient`, HTTP Interfaces |
| Resiliência | Resilience4j |
| Tracing | Micrometer Tracing + OpenTelemetry |

```yaml
# Spring Cloud Gateway
spring:
  cloud:
    gateway:
      routes:
        - id: produtos
          uri: http://produtos-service:8080
          predicates:
            - Path=/api/produtos/**
          filters:
            - name: CircuitBreaker
              args:
                name: produtosCB
                fallbackUri: forward:/fallback
```

**Padrões importantes**
- **Database per service**: cada serviço é dono dos seus dados.
- **Saga**: transações distribuídas por meio de eventos e compensações (orquestrada ou coreografada).
- **Outbox**: grava o evento na mesma transação do dado e publica depois.
- **CQRS e Event Sourcing**: separar leitura e escrita (use com critério).
- **Idempotência**: toda operação reentrante deve ser segura.
- **Contract testing** (Spring Cloud Contract, Pact).

> Comece com um **monólito modular** bem organizado. Migre para microsserviços só quando houver necessidade real de escala independente ou de times autônomos.

### 13.4 GraalVM Native Image

```bash
./mvnw -Pnative native:compile
./target/meu-app          # inicia em milissegundos, usa menos memória
```

Vantagens: *startup* rápido e baixo consumo de memória (bom para *serverless*). Desvantagens: build mais lento, reflection exige *hints* e menos flexibilidade dinâmica.

### 13.5 CI/CD (exemplo com GitHub Actions)

```yaml
name: CI
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 21
          cache: maven
      - run: ./mvnw -B verify
      - uses: docker/build-push-action@v5
        if: github.ref == 'refs/heads/main'
        with:
          push: false
          tags: meu-app:latest
```

### 13.6 Checklist de produção

- [ ] Perfis separados e segredos fora do repositório
- [ ] Migrations versionadas (Flyway/Liquibase) e `ddl-auto=validate`
- [ ] *Pool* de conexões dimensionado (HikariCP) e *timeouts* configurados
- [ ] Health checks, métricas e *tracing* habilitados
- [ ] Logs estruturados (JSON) com *correlation id*
- [ ] Tratamento global de erros sem vazar detalhes internos
- [ ] Limites de requisição (rate limiting) e validação de entrada
- [ ] Testes automatizados rodando no pipeline
- [ ] Estratégia de *graceful shutdown* (`server.shutdown=graceful`)
- [ ] Imagem Docker enxuta, executada sem root
- [ ] Dependências auditadas (Dependabot, OWASP Dependency-Check)

---

## Plano de estudos sugerido

| Etapa | Conteúdo | Duração sugerida |
|-------|----------|------------------|
| 1 | Módulo 1: Fundamentos | 2 semanas |
| 2 | Módulo 2: POO | 2 semanas |
| 3 | Módulo 3: Java intermediário | 3 semanas |
| 4 | Módulo 4: Java moderno | 1 semana |
| 5 | Módulos 6 e 7: Spring Boot básico e REST | 3 semanas |
| 6 | Módulo 8: JPA | 3 semanas |
| 7 | Módulos 9 e 10: Arquitetura e segurança | 2 semanas |
| 8 | Módulo 11: Testes | 2 semanas |
| 9 | Módulo 5: Java avançado (concorrência, JVM) | 2 semanas |
| 10 | Módulos 12 e 13: Avançado e produção | 4 semanas |

### Projeto final sugerido: **Plataforma de e-commerce**

Requisitos para aplicar tudo o que foi aprendido:

1. **Catálogo**: CRUD de produtos e categorias com paginação, filtros dinâmicos e cache.
2. **Usuários e segurança**: cadastro, login com JWT, papéis `USER` e `ADMIN`.
3. **Pedidos**: carrinho, finalização com transação, controle de estoque com *optimistic locking*.
4. **Pagamento**: integração com um serviço externo simulado, com *circuit breaker* e *retry*.
5. **Notificações**: eventos de domínio e mensageria (Kafka/RabbitMQ) para e-mail.
6. **Relatórios**: job agendado e processamento em lote.
7. **Qualidade**: cobertura mínima de 80% com testes unitários, de fatia e Testcontainers.
8. **Operação**: Docker Compose, Actuator, Prometheus/Grafana, pipeline de CI.
9. **Documentação**: OpenAPI e um README com decisões de arquitetura (ADRs).

---

## Referências recomendadas

- **Documentação oficial**: https://docs.oracle.com/en/java/ · https://docs.spring.io/spring-boot/
- **Livros**: *Effective Java* (Joshua Bloch), *Java Concurrency in Practice* (Brian Goetz), *Clean Code* e *Clean Architecture* (Robert C. Martin), *Spring in Action* (Craig Walls), *High-Performance Java Persistence* (Vlad Mihalcea).
- **Prática**: Exercism, LeetCode, projetos open source no GitHub.
- **Comunidade**: Baeldung, Spring Blog, JetBrains Academy, Inside.java.

> Dica final: não basta ler. **Digite os exemplos, quebre-os, corrija-os e construa projetos próprios.** O aprendizado vem da prática.

