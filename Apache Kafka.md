## Apache Kafka: Overview, Architecture, Use Cases, Setup, and Integration with Spring Boot

### Definition

Apache Kafka is an open-source distributed event streaming platform capable of handling high throughput of data. It was originally developed by LinkedIn and is now part of the Apache Software Foundation. Kafka is used for building real-time data pipelines and streaming applications.

### Architecture

Kafka's architecture is designed for high scalability and fault tolerance. Here are the key components:

1. **Producers**: Applications that publish (write) data to Kafka topics.
2. **Consumers**: Applications that subscribe to (read) data from Kafka topics.
3. **Brokers**: Kafka servers that store data and serve clients. A Kafka cluster is composed of multiple brokers.
4. **Topics**: Logical channels to which producers send records and from which consumers receive records. Topics can be partitioned.
5. **Partitions**: Sub-divisions of a topic that allow Kafka to scale horizontally and provide fault tolerance. Each partition is an ordered, immutable sequence of records.
6. **Zookeeper**: Used for managing and coordinating Kafka brokers. It helps in leader election, configuration management, and cluster state management.

### Kafka Architecture Diagram

```plaintext
+-----------------------------------+
|            Kafka Cluster          |
|                                   |
| +------------+    +------------+  |
| |   Broker   |    |   Broker   |  |
| | (Partition |    | (Partition |  |
| |  Replica 1)|    |  Replica 2)|  |
| +------------+    +------------+  |
|                                   |
+-----------------------------------+
```

### Advantages

1. **High Throughput**: Kafka can handle high volume and high velocity of data, making it suitable for large-scale message processing.
2. **Scalability**: Kafka can scale horizontally by adding more brokers and partitions.
3. **Durability**: Kafka uses a distributed commit log to ensure data durability.
4. **Fault Tolerance**: Data replication across multiple brokers ensures high availability and fault tolerance.
5. **Real-time Processing**: Kafka supports real-time data streaming and processing.
6. **Decoupling of Data Streams**: Producers and consumers are decoupled, allowing independent scaling and development.

### Disadvantages

1. **Complexity**: Setting up and managing Kafka clusters can be complex and requires careful planning.
2. **Operational Overhead**: Managing a Kafka cluster requires monitoring, tuning, and maintenance.
3. **Latency**: While Kafka is designed for low-latency operations, network issues and improper configuration can introduce latency.
4. **Learning Curve**: Kafka has a steep learning curve for newcomers due to its distributed nature and configuration complexities.

### Use Cases

1. **Real-time Data Streaming**: For applications that require real-time data processing, such as financial services or online retail.
2. **Event Sourcing**: For applications that use event-driven architecture.
3. **Log Aggregation**: Collecting and managing logs from various sources.
4. **Data Integration**: As a data integration layer for connecting various data sources.
5. **Metrics Collection**: Gathering operational metrics for monitoring and analysis.
6. **Stream Processing**: Applications like fraud detection, recommendation engines, and real-time analytics.

### Setting Up Kafka

#### Prerequisites

- Java 8+ installed
- Zookeeper (Kafka uses Zookeeper for cluster management)

#### Steps

1. **Download Kafka**:
   ```sh
   wget https://downloads.apache.org/kafka/2.8.0/kafka_2.13-2.8.0.tgz
   tar -xzf kafka_2.13-2.8.0.tgz
   cd kafka_2.13-2.8.0
   ```

2. **Start Zookeeper**:
   ```sh
   bin/zookeeper-server-start.sh config/zookeeper.properties
   ```

3. **Start Kafka Broker**:
   ```sh
   bin/kafka-server-start.sh config/server.properties
   ```

4. **Create a Topic**:
   ```sh
   bin/kafka-topics.sh --create --topic test --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
   ```

5. **List Topics**:
   ```sh
   bin/kafka-topics.sh --list --bootstrap-server localhost:9092
   ```

6. **Start a Producer**:
   ```sh
   bin/kafka-console-producer.sh --topic test --bootstrap-server localhost:9092
   ```

7. **Start a Consumer**:
   ```sh
   bin/kafka-console-consumer.sh --topic test --from-beginning --bootstrap-server localhost:9092
   ```

### Kafka Cluster Setup

1. **Modify Server Properties**: Edit `config/server.properties` for each broker to set unique broker IDs and log directories.
   ```properties
   broker.id=1
   log.dirs=/tmp/kafka-logs-1
   ```

2. **Start Brokers**:
   ```sh
   bin/kafka-server-start.sh config/server.properties
   ```

3. **Cluster Information**:
   - Kafka stores metadata information in Zookeeper.
   - Use Zookeeper to check the status of the cluster.

### Integrating Kafka with Spring Boot

#### Prerequisites

- Spring Boot application
- Maven or Gradle build tool

#### Steps

1. **Add Dependencies**:
   - Add the following dependencies to your `pom.xml` (for Maven):
     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter</artifactId>
     </dependency>
     <dependency>
         <groupId>org.springframework.kafka</groupId>
         <artifactId>spring-kafka</artifactId>
     </dependency>
     ```

   - Or to `build.gradle` (for Gradle):
     ```groovy
     implementation 'org.springframework.boot:spring-boot-starter'
     implementation 'org.springframework.kafka:spring-kafka'
     ```

2. **Kafka Configuration**:
   - Create a configuration class to set up Kafka producer and consumer.
   
   ```java
   @Configuration
   public class KafkaConfig {

       @Value("${kafka.bootstrap-servers}")
       private String bootstrapServers;

       @Bean
       public ProducerFactory<String, String> producerFactory() {
           Map<String, Object> configProps = new HashMap<>();
           configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
           configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
           configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
           return new DefaultKafkaProducerFactory<>(configProps);
       }

       @Bean
       public KafkaTemplate<String, String> kafkaTemplate() {
           return new KafkaTemplate<>(producerFactory());
       }

       @Bean
       public ConsumerFactory<String, String> consumerFactory() {
           Map<String, Object> props = new HashMap<>();
           props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
           props.put(ConsumerConfig.GROUP_ID_CONFIG, "group_id");
           props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
           props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
           return new DefaultKafkaConsumerFactory<>(props);
       }

       @Bean
       public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
           ConcurrentKafkaListenerContainerFactory<String, String> factory =
                   new ConcurrentKafkaListenerContainerFactory<>();
           factory.setConsumerFactory(consumerFactory());
           return factory;
       }
   }
   ```

3. **Producer Service**:
   - Create a service to send messages to Kafka topics.
   
   ```java
   @Service
   public class KafkaProducerService {
   
       @Autowired
       private KafkaTemplate<String, String> kafkaTemplate;
   
       public void sendMessage(String topic, String message) {
           kafkaTemplate.send(topic, message);
       }
   }
   ```

4. **Consumer Service**:
   - Create a service to consume messages from Kafka topics.
   
   ```java
   @Service
   public class KafkaConsumerService {
   
       @KafkaListener(topics = "test", groupId = "group_id")
       public void listen(String message) {
           System.out.println("Received Message: " + message);
       }
   }
   ```

5. **Application Properties**:
   - Add Kafka properties in `application.properties`.
   
   ```properties
   kafka.bootstrap-servers=localhost:9092
   ```

6. **Controller**:
   - Create a REST controller to trigger Kafka message sending.
   
   ```java
   @RestController
   @RequestMapping("/kafka")
   public class KafkaController {
   
       @Autowired
       private KafkaProducerService kafkaProducerService;
   
       @PostMapping("/publish")
       public ResponseEntity<String> publish(@RequestParam("message") String message) {
           kafkaProducerService.sendMessage("test", message);
           return ResponseEntity.ok("Message sent to Kafka");
       }
   }
   ```
### Real-Time Example: E-Commerce Order Processing System

Imagine an e-commerce platform where various microservices are responsible for different aspects of order processing, such as order placement, payment, inventory management, and shipping.


```plaintext
+----------------------------------------------------------------------------------------------------+
|                                        Kafka Cluster                                               |
| +------------------+            +------------------+            +------------------+              |
| |      Broker 1    |            |      Broker 2    |            |      Broker 3    |              |
| | +--------------+ |            | +--------------+ |            | +--------------+ |              |
| | | Partition 0  | |            | | Partition 0  | |            | | Partition 0  | |              |
| | | (Leader)     | |            | | (Replica)    | |            | | (Replica)    | |              |
| | +--------------+ |            | +--------------+ |            | +--------------+ |              |
| | +--------------+ |            | +--------------+ |            | +--------------+ |              |
| | | Partition 1  | |            | | Partition 1  | |            | | Partition 1  | |              |
| | | (Replica)    | |            | | (Leader)     | |            | | (Replica)    | |              |
| | +--------------+ |            | +--------------+ |            | +--------------+ |              |
| | +--------------+ |            | +--------------+ |            | +--------------+ |              |
| | | Partition 2  | |            | | Partition 2  | |            | | Partition 2  | |              |
| | | (Replica)    | |            | | (Replica)    | |            | | (Leader)     | |              |
| | +--------------+ |            | +--------------+ |            | +--------------+ |              |
| +------------------+            +------------------+            +------------------+              |
+----------------------------------------------------------------------------------------------------+
       |                               |                               |                            |
       |                               |                               |                            |
       v                               v                               v                            |
+--------------------+      +--------------------+      +--------------------+                      |
|   Producer 1       |      |   Producer 2       |      |   Producer 3       |                      |
|   (Order Service)  |      |   (Payment Service)|      | (Inventory Service)|                      |
+--------------------+      +--------------------+      +--------------------+                      |
       |                               |                               |                            |
       v                               v                               v                            |
+----------------------------------------------------------------------------------------------------+
|                                            Topics                                                 |
| +--------------------+      +--------------------+      +--------------------+                    |
| |      Topic 1       |      |      Topic 2       |      |      Topic 3       |                    |
| | (Orders)           |      | (Payments)         |      | (Inventory)        |                    |
| +--------------------+      +--------------------+      +--------------------+                    |
| | P0 | P1 | P2 | P3  |      | P0 | P1 | P2 | P3  |      | P0 | P1 | P2 | P3  |                    |
| +----+----+----+-----+      +----+----+----+-----+      +----+----+----+-----+                    |
+----------------------------------------------------------------------------------------------------+
       |                               |                               |                            |
       v                               v                               v                            |
+--------------------+      +--------------------+      +--------------------+                      |
|   Consumer 1       |      |   Consumer 2       |      |   Consumer 3       |                      |
|  (Shipping Service)|      |   (Billing Service)|      | (Warehouse Service)|                      |
+--------------------+      +--------------------+      +--------------------+                      |
       |                               |                               |                            |
       v                               v                               v                            |
+----------------------------------------------------------------------------------------------------+
|                                          Zookeeper                                                 |
|   +------------------+                  +------------------+                 +------------------+  |
|   |  Zookeeper Node 1|                  | Zookeeper Node 2 |                 | Zookeeper Node 3 |  |
|   +------------------+                  +------------------+                 +------------------+  |
|                                                                                                    |
+----------------------------------------------------------------------------------------------------+
``` 
#### Producers

- **Definition**: Producers are client applications that publish (write) data to Kafka topics.
- **Role in Architecture**: In the architecture diagram, `Producer 1` (Order Service), `Producer 2` (Payment Service), and `Producer 3` (Inventory Service) are producing messages to Kafka topics such as "Orders", "Payments", and "Inventory".
- **Function**: Producers send data to Kafka brokers, specifying the topic to which the data should be written.
```java
public class OrderService {
    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void placeOrder(Order order) {
        // Convert order object to JSON
        String orderJson = convertOrderToJson(order);
        kafkaTemplate.send("orders", orderJson);
    }
}
```

#### Consumers

- **Definition**: Consumers are client applications that subscribe to (read) data from Kafka topics.
- **Role in Architecture**: In the architecture diagram, `Consumer 1` (Shipping Service), `Consumer 2` (Billing Service), and `Consumer 3` (Warehouse Service) are consuming messages from Kafka topics like "Orders", "Payments", and "Inventory".
- **Function**: Consumers read data from Kafka brokers, processing the data according to the application's needs. Each consumer belongs to a consumer group, ensuring that each message is consumed by only one consumer within the group.
```java
public class PaymentService {
    @KafkaListener(topics = "orders", groupId = "payment-group")
    public void processOrder(String orderJson) {
        Order order = convertJsonToOrder(orderJson);
        // Process payment
    }
}
```

#### Brokers

- **Definition**: Brokers are Kafka servers that store data and serve client requests.
- **Role in Architecture**: In the architecture diagram, there are three brokers (Broker 1, Broker 2, Broker 3) forming the Kafka cluster.
- **Function**: Brokers manage data storage and retrieval, handle replication, and maintain metadata about the topics and partitions. They communicate with producers, consumers, and Zookeeper.

#### Topics

- **Definition**: Topics are logical channels to which producers send data and from which consumers receive data.
- **Role in Architecture**: Topics like "Orders", "Payments", and "Inventory" organize the data streams in Kafka.
- **Function**: Topics provide a way to categorize and manage different types of data. Each topic can have multiple partitions to allow for parallel processing.

#### Partitions

- **Definition**: Partitions are sub-divisions of topics that enable parallelism and fault tolerance.
- **Role in Architecture**: Each topic has multiple partitions (P0, P1, P2, P3) distributed across brokers.
- **Function**: Partitions allow Kafka to scale horizontally by distributing data across multiple brokers. Each partition is an ordered, immutable sequence of records, and each record within a partition has a unique offset.

#### Zookeeper

- **Definition**: Zookeeper is a centralized service for maintaining configuration information, naming, providing distributed synchronization, and providing group services.
- **Role in Architecture**: Zookeeper nodes (Zookeeper Node 1, Zookeeper Node 2, Zookeeper Node 3) manage the Kafka brokers.
- **Function**: Zookeeper handles the coordination of Kafka brokers, including leader election for partitions, cluster metadata management, and configuration management.

#### Offsets

- **Definition**: Offsets are unique identifiers assigned to each record within a partition.
- **Role in Architecture**: Offsets track the position of each message within a partition, enabling consumers to know which messages have been processed.
- **Function**: Consumers use offsets to keep track of their progress in reading messages from a partition, allowing them to resume from where they left off in case of a failure.

### Data Flow in Apache Kafka

1. **Producers to Brokers**:
   - Producers (`OrderService`, `PaymentService`, `InventoryService`) publish messages to specific Kafka topics (`Orders`, `Payments`, `Inventory`).
   - Each message is sent to a specific partition within the topic based on a partitioning strategy (e.g., round-robin, key-based).

2. **Broker Storage and Replication**:
   - Kafka brokers receive messages and store them in the appropriate partition.
   - Each partition has a leader broker responsible for handling read and write requests, and replicas stored on other brokers for fault tolerance.
   - For example, Partition 0 of the "Orders" topic might be the leader on Broker 1 and have replicas on Broker 2 and Broker 3.

3. **Consumers from Brokers**:
   - Consumers (`ShippingService`, `BillingService`, `WarehouseService`) subscribe to Kafka topics and read messages from the partitions.
   - Each consumer maintains its offset, tracking the messages it has already processed.
   - For example, `ShippingService` might read messages from Partition 0 of the "Orders" topic, process the order, and commit the offset to indicate successful processing.

4. **Coordination via Zookeeper**:
   - Zookeeper manages the Kafka cluster, keeping track of broker metadata, partition leaders, and configuration changes.
   - It ensures high availability and synchronization across the cluster.

### Reasons for Data Flow Order

1. **Producer to Broker**: Ensures that data is stored reliably and can be distributed across the cluster for scalability and fault tolerance.
2. **Broker to Consumer**: Enables consumers to process data in real-time or batch mode, ensuring they get the most up-to-date information.
3. **Partitioning**: Provides scalability and parallelism, allowing Kafka to handle high volumes of data and enabling multiple consumers to process data concurrently.
4. **Offsets**: Ensure exactly-once or at-least-once delivery semantics, allowing consumers to track their progress and recover from failures.

### Setting Up and Configuring Kafka for the Example

1. **Download and Install Kafka**:
   ```sh
   wget https://downloads.apache.org/kafka/2.8.0/kafka_2.13-2.8.0.tgz
   tar -xzf kafka_2.13-2.8.0.tgz
   cd kafka_2.13-2.8.0
   ```

2. **Start Zookeeper**:
   ```sh
   bin/zookeeper-server-start.sh config/zookeeper.properties
   ```

3. **Start Kafka Broker**:
   ```sh
   bin/kafka-server-start.sh config/server.properties
   ```

4. **Create Topics**:
   ```sh
   bin/kafka-topics.sh --create --topic orders --bootstrap-server localhost:9092 --partitions 3 --replication-factor 2
   ```

5. **Start Producers and Consumers**: 
   - Start the `OrderService` to produce messages to the "orders" topic.
   - Start the `PaymentService`, `InventoryService`, and `ShippingService` to consume messages from the "orders" topic.

#### Producer Service

Create a service to send messages to Kafka topics:

```java
@Service
public class OrderService {
    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    public void placeOrder(Order order) {
        // Convert order object to JSON
        String orderJson = convertOrderToJson(order);
        kafkaTemplate.send("orders", orderJson);
    }
}
```

#### Consumer Service

Create a service to consume messages from Kafka topics:

```java
@Service
public class PaymentService {
    @KafkaListener(topics = "orders", groupId = "payment-group")
    public void processOrder(String orderJson) {
        Order order = convertJsonToOrder(orderJson);
        // Process payment
    }
}
```

#### Application Properties

Add Kafka properties in `application.properties`:

```properties
kafka.bootstrap-servers=localhost:9092
```

#### Controller

Create a REST controller to trigger Kafka message sending:

```java
@RestController
@RequestMapping("/kafka")
public class KafkaController {
    @Autowired
    private OrderService orderService;

    @PostMapping("/publish")
    public ResponseEntity<String> publish(@RequestParam("message") String message) {
        Order order = new Order(message); // Simplified example
        orderService.placeOrder(order);
        return ResponseEntity.ok("Order placed");
    }
}
```
### Conclusion

By following these steps, you can set up Kafka, create a Kafka cluster, define topics, and integrate Kafka with a Spring Boot application. This enables you to build scalable, high-throughput, and fault-tolerant data streaming applications.
