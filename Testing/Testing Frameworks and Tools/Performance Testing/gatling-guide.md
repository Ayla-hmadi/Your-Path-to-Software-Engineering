# Gatling Performance Testing Guide

## Overview

### Introduction
```yaml
Description:
  Modern load testing tool designed for continuous integration and complex performance testing

Key Features:
  - Scala DSL
  - Real-time metrics
  - HTTP/S support
  - Asynchronous architecture
  - CI/CD integration
  - Rich reporting
```

### Installation
```bash
# Download Gatling
wget https://repo1.maven.org/maven2/io/gatling/highcharts/gatling-charts-highcharts-bundle/3.9.0/gatling-charts-highcharts-bundle-3.9.0-bundle.zip

# Maven setup
<dependency>
    <groupId>io.gatling.highcharts</groupId>
    <artifactId>gatling-charts-highcharts</artifactId>
    <version>3.9.0</version>
    <scope>test</scope>
</dependency>
```

## Test Scripts

### Basic Structure
```scala
class BasicSimulation extends Simulation {
  val httpProtocol = http
    .baseUrl("http://example.com")
    .acceptHeader("application/json")
    
  val scn = scenario("Basic Scenario")
    .exec(http("request_1")
      .get("/api/users"))
    .pause(1)
    
  setUp(
    scn.inject(atOnceUsers(10))
  ).protocols(httpProtocol)
}
```

### HTTP Requests
```scala
// GET request
http("Get Users")
  .get("/api/users")
  .check(status.is(200))

// POST request with body
http("Create User")
  .post("/api/users")
  .header("Content-Type", "application/json")
  .body(StringBody("""{"name": "John"}"""))
  .check(jsonPath("$.id").saveAs("userId"))
```

## Load Profiles

### Injection Patterns
```scala
// Basic patterns
setUp(
  scn.inject(
    nothingFor(4 seconds),
    atOnceUsers(10),
    rampUsers(10) during(5 seconds),
    constantUsersPerSec(20) during(15 seconds),
    constantUsersPerSec(20) during(15 seconds) randomized
  )
)

// Complex patterns
setUp(
  scn.inject(
    incrementUsersPerSec(2)
      .times(5)
      .eachLevelLasting(10 seconds)
      .separatedByRampsLasting(10 seconds)
      .startingFrom(10)
  )
)
```

### Throttling
```scala
setUp(
  scn.inject(constantUsersPerSec(100) during(30 minutes))
).throttle(
  reachRps(100) in(10 seconds),
  holdFor(1 minute),
  jumpToRps(50),
  holdFor(2 minutes)
)
```

## Checks and Assertions

### Response Validation
```scala
// Status checks
.check(status.is(200))
.check(status.not(404))

// JSON checks
.check(
  jsonPath("$.name").is("John"),
  jsonPath("$.age").ofType[Int].gt(18),
  jsonPath("$.items[*]").count.is(5)
)

// XPath checks
.check(
  xpath("//user/name").is("John"),
  xpath("//user/id").saveAs("userId")
)
```

### Assertions
```scala
setUp(scn).assertions(
  global.responseTime.max.lt(3000),
  global.successfulRequests.percent.gt(95),
  details("Create User").responseTime.percentile3.lt(1000),
  global.failedRequests.count.is(0)
)
```

## Scenarios

### Scenario Building
```scala
val scn = scenario("User Flow")
  .exec(http("Home")
    .get("/"))
  .pause(1)
  .exec(http("Login")
    .post("/login")
    .formParam("username", "user")
    .formParam("password", "pass"))
  .pause(2)
  .group("User Actions") {
    exec(http("View Profile")
      .get("/profile"))
      .pause(1)
      .exec(http("Update Profile")
        .put("/profile")
        .body(StringBody("""{"status": "active"}""")))
  }
```

### Feeders
```scala
// CSV feeder
val csvFeeder = csv("users.csv").random

// Custom feeder
val customFeeder = Iterator.continually(Map(
  "username" -> s"user${Random.nextInt(100)}",
  "password" -> "pass123"
))

scenario("Feeder Test")
  .feed(csvFeeder)
  .exec(http("Login Request")
    .post("/login")
    .formParam("username", "${username}")
    .formParam("password", "${password}"))
```

## Advanced Features

### Session Handling
```scala
// Session manipulation
exec(session => {
  val userId = session("userId").as[String]
  session.set("modifiedId", s"user-$userId")
})

// Conditional execution
doIf("${userId.exists()}") {
  exec(http("Get User Details")
    .get("/users/${userId}"))
}
```

### Loops and Conditions
```scala
// Repeat
repeat(5) {
  exec(http("Repeated Request")
    .get("/api/item/${itemId}"))
}

// While loop
asLongAs("${condition}") {
  exec(http("While Request")
    .get("/api/check"))
}

// Forever loop
forever {
  exec(http("Forever Request")
    .get("/api/ping"))
}
```

## Reports and Monitoring

### Report Configuration
```scala
// Report generation
gatling.conf content:

gatling {
  charting {
    indicators {
      percentile1 = 50
      percentile2 = 75
      percentile3 = 95
      percentile4 = 99
    }
  }
}
```

### Monitoring Integration
```scala
// Graphite integration
gatling {
  data {
    writers = [console, file, graphite]
    graphite {
      host = "localhost"
      port = 2003
      protocol = "tcp"
      rootPathPrefix = "gatling"
    }
  }
}
```

## Best Practices

### Test Design
```yaml
Guidelines:
  - Clear scenario structure
  - Appropriate pauses
  - Resource cleanup
  - Error handling
  - Realistic data

Anti-patterns:
  - Hardcoded values
  - Missing checks
  - Unrealistic load
  - Resource leaks
```

### Performance Optimization
```yaml
Optimization Areas:
  Script Design:
    - Efficient checks
    - Minimal session data
    - Proper feeders
    
  Execution:
    - JVM tuning
    - Resource monitoring
    - Results handling
```
