# Java Ecosystem and Libraries

## Core Libraries

### Java Standard Edition (SE)
1. **Collections Framework**
   ```java
   // Core collections
   List<String> list = new ArrayList<>();
   Set<Integer> set = new HashSet<>();
   Map<String, Object> map = new HashMap<>();
   
   // Specialized collections
   Queue<Task> queue = new LinkedList<>();
   Deque<String> deque = new ArrayDeque<>();
   NavigableSet<Integer> treeSet = new TreeSet<>();
   ```

2. **IO and NIO**
   ```java
   // File operations
   Path path = Paths.get("file.txt");
   try (BufferedReader reader = Files.newBufferedReader(path)) {
       String line;
       while ((line = reader.readLine()) != null) {
           // Process line
       }
   }
   
   // NIO channels
   try (FileChannel channel = FileChannel.open(path)) {
       ByteBuffer buffer = ByteBuffer.allocate(1024);
       channel.read(buffer);
   }
   ```

### Concurrent Utilities
1. **Thread Management**
   ```java
   // Executor framework
   ExecutorService executor = Executors.newFixedThreadPool(4);
   
   Future<String> future = executor.submit(() -> {
       // Async task
       return "Result";
   });
   
   // CompletableFuture
   CompletableFuture<String> cf = CompletableFuture
       .supplyAsync(() -> fetchData())
       .thenApply(data -> processData(data))
       .thenCompose(result -> saveData(result));
   ```

2. **Concurrent Collections**
   ```java
   // Thread-safe collections
   ConcurrentHashMap<String, Object> concurrentMap = new ConcurrentHashMap<>();
   BlockingQueue<Task> taskQueue = new LinkedBlockingQueue<>();
   CopyOnWriteArrayList<String> copyOnWriteList = new CopyOnWriteArrayList<>();
   ```

## Enterprise Libraries

### Spring Framework
1. **Spring Boot**
   ```java
   @SpringBootApplication
   public class Application {
       public static void main(String[] args) {
           SpringApplication.run(Application.class, args);
       }
   }
   
   @RestController
   public class UserController {
       @Autowired
       private UserService userService;
       
       @GetMapping("/users")
       public List<User> getUsers() {
           return userService.findAll();
       }
   }
   ```

2. **Spring Data**
   ```java
   @Repository
   public interface UserRepository 
       extends JpaRepository<User, Long> {
       
       List<User> findByAgeGreaterThan(int age);
       
       @Query("SELECT u FROM User u WHERE u.status = :status")
       List<User> findByCustomQuery(@Param("status") String status);
   }
   ```

### Jakarta EE
1. **Enterprise Components**
   ```java
   @Stateless
   public class OrderProcessor {
       @PersistenceContext
       private EntityManager em;
       
       @TransactionAttribute(TransactionAttributeType.REQUIRED)
       public void processOrder(Order order) {
           em.persist(order);
       }
   }
   ```

2. **JPA (Java Persistence API)**
   ```java
   @Entity
   @Table(name = "users")
   public class User {
       @Id
       @GeneratedValue(strategy = GenerationType.IDENTITY)
       private Long id;
       
       @Column(nullable = false)
       private String name;
       
       @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
       private List<Order> orders;
   }
   ```

## Testing Frameworks

### JUnit
1. **Unit Testing**
   ```java
   @Test
   public void testCalculation() {
       Calculator calc = new Calculator();
       assertEquals(4, calc.add(2, 2));
       assertThrows(IllegalArgumentException.class, 
           () -> calc.divide(1, 0));
   }
   
   @ParameterizedTest
   @CsvSource({"1,1,2", "2,3,5", "5,8,13"})
   void testAddition(int a, int b, int expected) {
       assertEquals(expected, calculator.add(a, b));
   }
   ```

2. **Mockito**
   ```java
   @ExtendWith(MockitoExtension.class)
   class UserServiceTest {
       @Mock
       private UserRepository userRepository;
       
       @InjectMocks
       private UserService userService;
       
       @Test
       void testFindUser() {
           when(userRepository.findById(1L))
               .thenReturn(Optional.of(new User()));
           
           User user = userService.findById(1L);
           assertNotNull(user);
       }
   }
   ```

## Build Tools

### Maven
1. **Project Configuration**
   ```xml
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
           <version>${spring.boot.version}</version>
       </dependency>
   </dependencies>
   
   <build>
       <plugins>
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
           </plugin>
       </plugins>
   </build>
   ```

2. **Gradle**
   ```groovy
   plugins {
       id 'java'
       id 'org.springframework.boot' version '2.6.0'
   }
   
   dependencies {
       implementation 'org.springframework.boot:spring-boot-starter-web'
       testImplementation 'junit:junit:4.13.2'
   }
   ```

## Utility Libraries

### Apache Commons
1. **Commons Lang**
   ```java
   import org.apache.commons.lang3.StringUtils;
   
   // String operations
   String text = StringUtils.trimToEmpty(input);
   boolean isEmpty = StringUtils.isEmpty(text);
   
   // Object utilities
   String toString = ToStringBuilder.reflectionToString(object);
   ```

2. **Commons IO**
   ```java
   import org.apache.commons.io.FileUtils;
   import org.apache.commons.io.IOUtils;
   
   // File operations
   String content = FileUtils.readFileToString(file, "UTF-8");
   FileUtils.writeStringToFile(file, content, "UTF-8");
   ```

## Conclusion
Java's rich ecosystem of libraries and frameworks provides comprehensive solutions for various development needs, from enterprise applications to testing and utilities.
