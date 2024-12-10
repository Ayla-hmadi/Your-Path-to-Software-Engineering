# Gradle

## Introduction
Gradle is a flexible and powerful build automation tool that supports multiple languages and platforms. It provides a rich API for customizing builds through its Groovy or Kotlin-based Domain Specific Language (DSL).

## Basic Configuration

### Project Structure
```groovy
// build.gradle
plugins {
    id 'java'
    id 'org.springframework.boot' version '2.6.0'
    id 'io.spring.dependency-management' version '1.0.11.RELEASE'
}

group = 'com.example'
version = '1.0.0'
sourceCompatibility = '11'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

### Multi-Project Setup
```groovy
// settings.gradle
rootProject.name = 'my-project'
include 'core', 'api', 'web'

// build.gradle
allprojects {
    group = 'com.example'
    version = '1.0.0'
    
    repositories {
        mavenCentral()
    }
}

subprojects {
    apply plugin: 'java'
    
    dependencies {
        testImplementation 'junit:junit:4.13'
    }
}
```

## Dependency Management

### Configuration Types
```groovy
dependencies {
    // Main configurations
    implementation 'com.google.guava:guava:30.1-jre'
    compileOnly 'org.projectlombok:lombok:1.18.20'
    runtimeOnly 'org.postgresql:postgresql:42.2.23'
    
    // Test configurations
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.7.2'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.7.2'
    
    // Custom configurations
    customConfig 'org.apache.commons:commons-lang3:3.12.0'
}
```

### Version Management
```groovy
ext {
    versions = [
        springBoot: '2.6.0',
        junit     : '5.7.2',
        lombok    : '1.18.20'
    ]
}

dependencies {
    implementation "org.springframework.boot:spring-boot-starter:${versions.springBoot}"
    testImplementation "org.junit.jupiter:junit-jupiter-api:${versions.junit}"
}

// Version catalogs (Gradle 7.0+)
dependencies {
    implementation(platform('org.springframework.boot:spring-boot-dependencies:2.6.0'))
    implementation('org.springframework.boot:spring-boot-starter-web')
}
```

## Task Configuration

### Custom Tasks
```groovy
tasks.register('generateDocs') {
    group = 'documentation'
    description = 'Generates project documentation'
    
    doFirst {
        println "Starting documentation generation..."
    }
    
    doLast {
        File docsDir = file("$buildDir/docs")
        docsDir.mkdirs()
        // Generate documentation
    }
}

tasks.register('packageDistribution', Zip) {
    from 'build/libs'
    destinationDirectory = file('dist')
    archiveBaseName = 'distribution'
    archiveVersion = project.version
}
```

### Task Dependencies
```groovy
tasks.named('test') {
    dependsOn 'cleanTest'
    useJUnitPlatform()
    
    maxHeapSize = '1G'
    
    testLogging {
        events "passed", "skipped", "failed"
        exceptionFormat = 'full'
    }
    
    reports {
        html.enabled = true
        junitXml.enabled = true
    }
}
```

## Build Lifecycle

### Hooks and Phases
```groovy
gradle.buildFinished {
    println "Build completed"
}

afterEvaluate {
    tasks.named('compileJava') {
        println "Compiling Java source files..."
    }
}

tasks.named('build') {
    doFirst {
        println "Starting build..."
    }
    doLast {
        println "Build finished."
    }
}
```

## Plugin Development

### Custom Plugin
```groovy
class CustomPlugin implements Plugin<Project> {
    void apply(Project project) {
        // Create extension
        project.extensions.create('customConfig', CustomExtension)
        
        // Register tasks
        project.tasks.register('customTask') {
            group = 'custom'
            description = 'Custom task example'
            
            doLast {
                println project.customConfig.message
            }
        }
    }
}

class CustomExtension {
    String message = 'Default message'
}

// Apply plugin
apply plugin: CustomPlugin
```

## Performance Optimization

### Build Cache
```groovy
buildCache {
    local {
        enabled = true
        directory = "${rootDir}/build-cache"
        removeUnusedEntriesAfterDays = 30
    }
    
    remote(HttpBuildCache) {
        url = 'https://cache.example.com/'
        credentials {
            username = 'cache-user'
            password = 'cache-password'
        }
    }
}
```

### Parallel Execution
```groovy
org.gradle.parallel=true
org.gradle.caching=true
org.gradle.configureondemand=true
org.gradle.daemon=true
org.gradle.jvmargs=-Xmx2g -XX:MaxMetaspaceSize=512m -XX:+HeapDumpOnOutOfMemoryError
```

## Best Practices

### Project Organization
```groovy
// Separate configuration logic
apply from: 'gradle/dependencies.gradle'
apply from: 'gradle/quality.gradle'
apply from: 'gradle/testing.gradle'

// Consistent structure
project(':core') {
    dependencies {
        implementation project(':common')
    }
}

// Version management
configurations.all {
    resolutionStrategy {
        failOnVersionConflict()
        preferProjectModules()
    }
}
```

## Conclusion
Gradle provides a flexible and powerful build system with extensive customization capabilities, making it suitable for complex build automation needs.
