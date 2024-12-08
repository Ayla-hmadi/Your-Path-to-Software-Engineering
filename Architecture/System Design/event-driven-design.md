# Event-Driven Architecture

## Introduction
Event-Driven Architecture (EDA) is a software design pattern where the production, detection, consumption, and reaction to events drive the system's behavior. Events represent a significant change in state and flow through the system as messages.

## Core Concepts

### Events
1. **Event Definition**
   - State change notification
   - Immutable record
   - Time-stamped occurrence
   - Business significance
   - Message format

2. **Event Structure**
   ```java
   public class Event {
       private String eventId;
       private String eventType;
       private LocalDateTime timestamp;
       private String source;
       private Map<String, Object> data;
       
       // Constructor, getters, setters
   }
   
   // Specific event example
   public class OrderCreatedEvent extends Event {
       private String orderId;
       private String customerId;
       private BigDecimal orderTotal;
       
       // Constructor, getters, setters
   }
   ```

## Architecture Components

### Event Producers
1. **Producer Implementation**
   ```java
   @Service
   public class OrderService {
       @Autowired
       private EventPublisher eventPublisher;
       
       public Order createOrder(OrderRequest request) {
           // Create order
           Order order = orderRepository.save(new Order(request));
           
           // Publish event
           OrderCreatedEvent event = new OrderCreatedEvent(order);
           eventPublisher.publish(event);
           
           return order;
       }
   }
   ```

2. **Publishing Mechanisms**
   ```java
   @Component
   public class KafkaEventPublisher implements EventPublisher {
       @Autowired
       private KafkaTemplate<String, Event> kafkaTemplate;
       
       @Override
       public void publish(Event event) {
           kafkaTemplate.send("events-topic", event.getEventId(), event);
       }
   }
   ```

### Event Consumers
1. **Consumer Implementation**
   ```java
   @Service
   public class OrderEventConsumer {
       @KafkaListener(topics = "orders-topic")
       public void handleOrderCreated(OrderCreatedEvent event) {
           // Process order created event
           processOrder(event);
       }
       
       @KafkaListener(topics = "orders-topic")
       public void handleOrderCancelled(OrderCancelledEvent event) {
           // Process order cancelled event
           cancelOrder(event);
       }
   }
   ```

2. **Event Processing**
   ```java
   @Component
   public class EventProcessor {
       @Autowired
       private List<EventHandler> eventHandlers;
       
       public void processEvent(Event event) {
           eventHandlers.stream()
               .filter(handler -> handler.canHandle(event))
               .forEach(handler -> handler.handle(event));
       }
   }
   ```

## Event Patterns

### Publish-Subscribe Pattern
1. **Topic-Based**
   ```java
   @Configuration
   public class KafkaConfig {
       @Bean
       public KafkaTemplate<String, Event> kafkaTemplate() {
           return new KafkaTemplate<>(producerFactory());
       }
       
       @Bean
       public ConsumerFactory<String, Event> consumerFactory() {
           Map<String, Object> config = new HashMap<>();
           // Configure consumer properties
           return new DefaultKafkaConsumerFactory<>(config);
       }
   }
   ```

2. **Content-Based**
   ```java
   @Component
   public class ContentBasedRouter {
       private Map<String, EventHandler> handlers;
       
       public void routeEvent(Event event) {
           String eventType = event.getEventType();
           EventHandler handler = handlers.get(eventType);
           if (handler != null) {
               handler.handle(event);
           }
       }
   }
   ```

### Event Sourcing
1. **Event Store**
   ```java
   @Repository
   public interface EventStore {
       void saveEvent(Event event);
       List<Event> getEvents(String aggregateId);
       List<Event> getEventsByType(String eventType);
   }
   
   @Repository
   public class EventStoreImpl implements EventStore {
       @Autowired
       private JdbcTemplate jdbcTemplate;
       
       @Override
       public void saveEvent(Event event) {
           String sql = "INSERT INTO events (event_id, event_type, aggregate_id, data) VALUES (?, ?, ?, ?)";
           jdbcTemplate.update(sql, event.getEventId(), event.getEventType(), 
                             event.getAggregateId(), serialize(event.getData()));
       }
   }
   ```

2. **Event Replay**
   ```java
   @Service
   public class AggregateService {
       @Autowired
       private EventStore eventStore;
       
       public Aggregate reconstructAggregate(String aggregateId) {
           List<Event> events = eventStore.getEvents(aggregateId);
           Aggregate aggregate = new Aggregate();
           
           events.forEach(event -> aggregate.apply(event));
           return aggregate;
       }
   }
   ```

## Implementation Strategies

### Message Brokers
1. **Kafka Implementation**
   ```java
   @Configuration
   public class KafkaProducerConfig {
       @Bean
       public ProducerFactory<String, Event> producerFactory() {
           Map<String, Object> config = new HashMap<>();
           config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
           config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
           config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
           return new DefaultKafkaProducerFactory<>(config);
       }
   }
   ```

2. **RabbitMQ Implementation**
   ```java
   @Configuration
   public class RabbitConfig {
       @Bean
       public Queue eventQueue() {
           return new Queue("event-queue", true);
       }
       
       @Bean
       public TopicExchange eventExchange() {
           return new TopicExchange("event-exchange");
       }
       
       @Bean
       public Binding binding(Queue queue, TopicExchange exchange) {
           return BindingBuilder.bind(queue)
               .to(exchange)
               .with("event.*");
       }
   }
   ```

### Error Handling
1. **Dead Letter Queues**
   ```java
   @Configuration
   public class DeadLetterConfig {
       @Bean
       public Queue deadLetterQueue() {
           return QueueBuilder.durable("dead-letter-queue")
               .withArgument("x-dead-letter-exchange", "")
               .withArgument("x-dead-letter-routing-key", "event-queue")
               .build();
       }
   }
   ```

2. **Retry Policies**
   ```java
   @Component
   public class RetryableEventHandler {
       @Retryable(maxAttempts = 3, backoff = @Backoff(delay = 1000))
       public void handleEvent(Event event) {
           // Process event with retry capability
       }
       
       @Recover
       public void recover(Exception e, Event event) {
           // Handle failed event after retries
       }
   }
   ```

## Best Practices

### Event Design
1. **Event Schema**
   - Version control
   - Backward compatibility
   - Forward compatibility
   - Schema registry
   - Documentation

2. **Event Validation**
   ```java
   @Component
   public class EventValidator {
       public void validateEvent(Event event) {
           Assert.notNull(event.getEventId(), "Event ID is required");
           Assert.notNull(event.getEventType(), "Event type is required");
           Assert.notNull(event.getTimestamp(), "Timestamp is required");
           // Additional validation
       }
   }
   ```

### System Monitoring
1. **Metrics Collection**
   ```java
   @Component
   public class EventMetrics {
       private final MeterRegistry registry;
       
       public EventMetrics(MeterRegistry registry) {
           this.registry = registry;
       }
       
       public void recordEventProcessed(String eventType) {
           registry.counter("events.processed", "type", eventType).increment();
       }
   }
   ```

2. **Logging Strategy**
   ```java
   @Aspect
   @Component
   public class EventLoggingAspect {
       private static final Logger logger = LoggerFactory.getLogger(EventLoggingAspect.class);
       
       @Around("execution(* com.example.event.EventHandler.handle(..))")
       public Object logEventProcessing(ProceedingJoinPoint joinPoint) throws Throwable {
           Event event = (Event) joinPoint.getArgs()[0];
           logger.info("Processing event: {}", event.getEventId());
           
           try {
               Object result = joinPoint.proceed();
               logger.info("Event processed successfully: {}", event.getEventId());
               return result;
           } catch (Exception e) {
               logger.error("Error processing event: {}", event.getEventId(), e);
               throw e;
           }
       }
   }
   ```

## Conclusion
Event-Driven Architecture provides a flexible and scalable approach to system design, but requires careful consideration of event design, processing patterns, and operational concerns for successful implementation.
