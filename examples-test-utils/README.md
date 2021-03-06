# Common Utils For Kogito Examples

Add the common utils dependency in the _pom.xml_ file:

```xml
<dependency>
  <groupId>org.kie.kogito</groupId>
  <artifactId>examples-test-utils</artifactId>
  <scope>test</scope>
</dependency>
```

## Infinispan Test Containers Support

### Usage in a Quarkus test:

Example:

```java
@QuarkusTest
@QuarkusTestResource(InfinispanQuarkusResource.class)
public class MyTest {
   // ...
}
```

And add the Infinispan properties in the _application.properties_:

```
#Infinispan
quarkus.infinispan-client.server-list=localhost:11222
quarkus.infinispan-client.use-auth=true
quarkus.infinispan-client.auth-username=admin
quarkus.infinispan-client.auth-password=admin
```

In case we want to run the container only if some requirements are met, we need to use it this way:

```java
@QuarkusTestResource(value = InfinispanQuarkusResource.class, initArgs = {@ResourceArg(name = "conditional", value = "true")})
```

### Usage in Spring Boot test:


```java
@Testcontainers
@ExtendWith(SpringExtension.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT, classes = DemoApplication.class)
@DirtiesContext(classMode = ClassMode.AFTER_EACH_TEST_METHOD) // reset spring context after each test method
public class MyTest {
    @Container
    public static GenericContainer<?> INFINISPAN = new InfinispanContainer();
    
    // ...
}
```

And add the Infinispan properties in the _application.properties_:

```
# Infinispan
infinispan.remote.server-list=localhost:11222
infinispan.remote.sasl-mechanism=PLAIN
infinispan.remote.auth-server-name=infinispan
infinispan.remote.use-auth=true
infinispan.remote.auth-realm=default
infinispan.remote.auth-username=admin
infinispan.remote.auth-password=admin
```

In case we want to run the container only if some requirements are met, we need to use it this way:

```java
@Container
public static GenericContainer<?> INFINISPAN = new InfinispanContainer().enableConditional();
```

## Keycloak Test Containers Support

### Usage in a Quarkus test:

Example:

```java
@QuarkusTest
@QuarkusTestResource(KeycloakQuarkusResource.class)
public class MyTest {
   // ...
}
```

### Usage in a Spring Boot test:

Example:

```java
@Testcontainers
@ExtendWith(SpringExtension.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT, classes = DemoApplication.class)
@DirtiesContext(classMode = ClassMode.AFTER_EACH_TEST_METHOD) // reset spring context after each test method
public class MyTest {
    @Container
    public static KeycloakContainer KEYCLOAK = new KeycloakContainer();
    
    // ...
}
```

## Kafka Test Containers Support

### Usage in a Quarkus test:

Example:

```java
@QuarkusTest
@QuarkusTestResource(KafkaQuarkusTestResource.class)
public class MyTest {
   
   @ConfigProperty(name = KafkaQuarkusTestResource.KAFKA_BOOTSTRAP_SERVERS)
   private String kafkaBootstrapServers;
   // ...
}
```

### Usage in a Spring Boot test:

Example:

```java
@Testcontainers
@ExtendWith(SpringExtension.class)
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT, classes = DemoApplication.class)
@DirtiesContext(classMode = ClassMode.AFTER_EACH_TEST_METHOD) // reset spring context after each test method
public class MyTest {
    @Container
    private static final KogitoKafkaContainer KAFKA = new KogitoKafkaContainer();
    
    // ...
}
```

## Kafka Client

Add the Kafka Client dependency in the _pom.xml_ file:

```xml
<dependency>
  <groupId>org.apache.kafka</groupId>
  <artifactId>kafka-clients</artifactId>
  <scope>test</scope>
</dependency>
```

And make use of it:

```java
kafkaClient = new KafkaClient(kafkaBootstrapServers);
kafkaClient.consume(TOPIC_CONSUMER, s -> {
            LOGGER.info("Received from kafka: {}", s);
            // ...
});
kafkaClient.produce(generateCloudEvent(traveller), TOPIC_PRODUCER);
kafkaClient.shutdown();
``` 
