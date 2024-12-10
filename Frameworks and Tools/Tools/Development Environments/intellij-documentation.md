# IntelliJ IDEA

## Introduction
IntelliJ IDEA is a powerful integrated development environment (IDE) primarily designed for Java and Kotlin development. It offers advanced code analysis, smart navigation, and extensive framework support.

## Core Features

### Project Structure
```text
project-root/
├── .idea/                 # IntelliJ configuration
├── src/
│   ├── main/
│   │   ├── java/         # Source code
│   │   ├── resources/    # Resource files
│   │   └── webapp/       # Web resources
│   └── test/
│       ├── java/         # Test code
│       └── resources/    # Test resources
├── target/               # Build output
└── pom.xml              # Project configuration
```

### IDE Configuration
```xml
<!-- .idea/workspace.xml -->
<project version="4">
  <component name="ProjectRunConfigurationManager">
    <configuration default="false" name="Application" type="SpringBootApplication">
      <module name="project-name" />
      <option name="SPRING_BOOT_MAIN_CLASS" value="com.example.Application" />
      <method v="2">
        <option name="Make" enabled="true" />
      </method>
    </configuration>
  </component>
</project>
```

## Code Intelligence

### Smart Code Completion
1. **Basic Completion**
   ```java
   public class Example {
       public void demonstration() {
           String text = "Hello";
           // Type 'text.' to see smart completion
           text.substring(0, 3);  // Smart completion suggests methods
       }
   }
   ```

2. **Advanced Completion**
   ```java
   @Service
   public class UserService {
       private final UserRepository userRepository;
       
       // Constructor injection with smart completion
       public UserService(UserRepository userRepository) {
           this.userRepository = userRepository;
       }
   }
   ```

## Debugging Features

### Debug Configuration
```xml
<configuration default="false" name="Debug Application" type="Remote">
  <option name="USE_SOCKET_TRANSPORT" value="true" />
  <option name="SERVER_MODE" value="false" />
  <option name="SHMEM_ADDRESS" />
  <option name="HOST" value="localhost" />
  <option name="PORT" value="5005" />
  <option name="AUTO_RESTART" value="false" />
  <method />
</configuration>
```

### Breakpoint Types
1. **Line Breakpoints**
   ```java
   public void processData() {
       // Regular breakpoint
       List<String> data = fetchData();
       
       // Conditional breakpoint
       for (String item : data) {
           if (item.startsWith("ERROR")) {  // Break condition
               processError(item);
           }
       }
   }
   ```

2. **Method Breakpoints**
   ```java
   // Break on method entry/exit
   @BreakpointHandler
   public void handleData(String data) {
       // Method implementation
   }
   ```

## Version Control Integration

### Git Configuration
```text
Version Control Settings:
1. Commit Settings
   - Show diff in commit dialog
   - Perform code analysis
   - Check TODO items
   - Optimize imports

2. Update Settings
   - Clean working tree
   - Update submodules
   - Show integrated changelists
```

### Commit Template
```text
# IntelliJ IDEA commit message template
<type>(<scope>): <subject>

<body>

<footer>

# Types: feat, fix, docs, style, refactor, test, chore
# Scope: auth, user, etc.
# Subject: imperative mood, no period
# Body: motivation for change, compare with previous
# Footer: breaking changes, closed issues
```

## Framework Integration

### Spring Framework
```xml
<!-- Spring configuration in IntelliJ -->
<configuration default="false" name="Spring Boot App">
  <option name="SPRING_BOOT_MAIN_CLASS" 
          value="com.example.Application" />
  <module name="project-name" />
  <envs>
    <env name="SPRING_PROFILES_ACTIVE" value="dev" />
  </envs>
  <method v="2">
    <option name="Make" enabled="true" />
  </method>
</configuration>
```

### Database Tools
```sql
-- Database Console Configuration
-- Name: Local Development Database
-- Host: localhost
-- Port: 5432
-- Database: dev_db
-- User: dev_user

-- Example query with completion
SELECT u.id, u.username, p.profile_name
FROM users u
JOIN profiles p ON u.id = p.user_id
WHERE u.status = 'ACTIVE';
```

## Performance Optimization

### Memory Settings
```text
# Custom VM options (-XX options)
-Xms2g                  # Initial heap size
-Xmx4g                  # Maximum heap size
-XX:ReservedCodeCacheSize=512m
-XX:+UseG1GC
-XX:SoftRefLRUPolicyMSPerMB=50
-XX:CICompilerCount=2
```

### Project Settings
```xml
<component name="PropertiesComponent">
  <property name="dynamic.classpath" value="true" />
  <property name="compiler.automake.allow.when.app.running" 
            value="true" />
  <property name="idea.max.intellisense.filesize" value="2500" />
</component>
```

## Productivity Features

### Live Templates
```java
// Custom live template for logging
/**
 * Template: logg
 * Expansion:
 */
private static final Logger logger = 
    LoggerFactory.getLogger($CLASS$.class);

// Template: test
/**
 * Expansion:
 */
@Test
public void test$NAME$() {
    $END$
}
```

### Code Generation
```java
// Generate using Alt+Insert (Windows/Linux) or Cmd+N (Mac)
public class GeneratedClass {
    private final String field1;
    private final int field2;
    
    // Generate: Constructor
    // Generate: Getters
    // Generate: equals() and hashCode()
    // Generate: toString()
}
```

## Conclusion
IntelliJ IDEA provides a comprehensive development environment with powerful features for Java and Kotlin development, extensive framework support, and intelligent coding assistance.
