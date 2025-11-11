# springboot-bestpractices
Spring Boot architecture is built on top of the Spring Framework and provides a streamlined way to create production-ready, stand-alone Java applications with minimal configuration.
 
Let’s go step-by-step 👇
 
 
---
 
🧱 1. Core Layers of Spring Boot Architecture
 
Spring Boot applications are typically divided into four main layers:
 
1️⃣ Presentation Layer (Web Layer)
 
Handles all HTTP requests and responses.
 
Contains controllers (usually annotated with @RestController or @Controller).
 
Responsible for routing requests to the right business logic.
 
Example:
 
@RestController
@RequestMapping("/api/pizza")
public class PizzaController {
    private final PizzaService pizzaService;
 
    public PizzaController(PizzaService pizzaService) {
        this.pizzaService = pizzaService;
    }
 
    @GetMapping
    public List<Pizza> getAllPizzas() {
        return pizzaService.getAllPizzas();
    }
}
 
 
 
---
 
2️⃣ Business Layer (Service Layer)
 
Contains the business logic of the application.
 
Uses @Service annotation.
 
Communicates between the controller and repository layers.
 
Example:
 
@Service
public class PizzaService {
    private final PizzaRepository pizzaRepository;
 
    public PizzaService(PizzaRepository pizzaRepository) {
        this.pizzaRepository = pizzaRepository;
    }
 
    public List<Pizza> getAllPizzas() {
        return pizzaRepository.findAll();
    }
}
 
 
 
---
 
3️⃣ Persistence Layer (Repository Layer)
 
Responsible for interacting with the database.
 
Uses Spring Data JPA, Hibernate, or JDBC.
 
Annotated with @Repository.
 
Example:
 
@Repository
public interface PizzaRepository extends JpaRepository<Pizza, Long> {
}
 
 
 
---
 
4️⃣ Database Layer
 
Actual database (e.g., MySQL, PostgreSQL, MongoDB).
 
Stores and retrieves data.
 
Managed by Spring Boot through ORM frameworks like Hibernate.
 
 
 
---
 
⚙️ 2. Auto-Configuration Layer
 
Spring Boot’s Auto-Configuration feature automatically configures beans and settings based on the dependencies in your pom.xml or build.gradle.
 
For example:
 
If you include spring-boot-starter-web, it automatically sets up Tomcat, Spring MVC, and JSON support.
 
If you include spring-boot-starter-data-jpa, it automatically configures Hibernate and DataSource.
 
 
Annotation used:
 
@SpringBootApplication
 
This is equivalent to:
 
@Configuration
@EnableAutoConfiguration
@ComponentScan
 
 
---
 
🚀 3. Embedded Server Layer
 
Spring Boot comes with an embedded application server, so you don’t need to deploy WAR files.
 
Common embedded servers:
 
Tomcat (default)
 
Jetty
 
Undertow
 
 
You can run your app directly using:
 
mvn spring-boot:run
 
or
 
java -jar app.jar
 
 
---
 
🔌 4. Dependency Injection (IoC Container)
 
Spring Boot uses Inversion of Control (IoC) to manage objects (beans) and their dependencies automatically.
 
Beans are managed by the Spring Container.
 
You define beans using annotations:
 
@Component, @Service, @Repository, @Controller
 
 
 
 
---
 
🧩 5. Configuration Layer
 
You can configure your app using:
 
application.properties or application.yml
 
Environment variables
 
Command-line arguments
 
 
Example:
 
server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/pizza_store
spring.datasource.username=root
spring.datasource.password=admin
 
 
---
 
🔄 6. Spring Boot Architecture Flow
 
Client → Controller → Service → Repository → Database
 
Here’s how a request flows:
 
1. The client sends an HTTP request (e.g., GET /api/pizza).
 
 
2. The controller receives the request and calls the service layer.
 
 
3. The service applies business logic and calls the repository.
 
 
4. The repository interacts with the database.
 
 
5. The result moves back up the chain → service → controller → client.
 
 
 
 
---
 
🧠 7. Optional Layers in Microservices
 
In microservice-based Spring Boot projects (like your Pizza Store Capstone), you may also include:
 
API Gateway – handles routing between services.
 
Discovery Server (Eureka) – tracks available microservices.
 
Config Server – manages externalized configurations.
 
Feign Client / RestTemplate / WebClient – for service-to-service calls.
 
Circuit Breaker (Resilience4j/Hystrix) – for fault tolerance.
 
 
 
---
 

 
Spring Boot REST APIs and Endpoints
 
🧩 What is a REST API?
 
A REST API (Representational State Transfer) allows communication between frontend (Angular) and backend (Spring Boot) using HTTP methods.
 
💡 Common HTTP Methods:
 
Method	Usage	Example
 
GET	Fetch data	/api/pizzas – get all pizzas
POST	Create new record	/api/orders – place a new order
PUT	Update existing record	/api/orders/{id} – update order
DELETE	Remove record	/api/orders/{id} – cancel order
 
 
🧠 Example — Pizza Controller:
 
@RestController
@RequestMapping("/api/pizzas")
public class PizzaController {
    
    @Autowired
    private PizzaService pizzaService;
 
    @GetMapping
    public List<Pizza> getAllPizzas() {
        return pizzaService.getAllPizzas();
    }
 
    @PostMapping
    public Pizza addPizza(@RequestBody Pizza pizza) {
        return pizzaService.addPizza(pizza);
    }
 
    @PutMapping("/{id}")
    public Pizza updatePizza(@PathVariable Long id, @RequestBody Pizza pizza) {
        return pizzaService.updatePizza(id, pizza);
    }
 
    @DeleteMapping("/{id}")
    public String deletePizza(@PathVariable Long id) {
        pizzaService.deletePizza(id);
        return "Pizza deleted successfully!";
    }
}
 
Each of these endpoints connects with the Angular frontend using HTTP requests.
 
What is a Singleton in Spring Boot?
 
A Singleton means only one instance of a class is created and shared across the entire Spring application context.
 
So when we say a bean is Singleton, it means:
 
> Spring creates only one object (instance) of that class, and the same instance is reused every time it is needed.
 
 
 
 
---
 
⚙️ How It Works in Spring Boot
 
When the Spring Boot application starts:
 
1. The IoC (Inversion of Control) Container scans your packages.
 
 
2. It finds all classes annotated with:
 
@Component
 
@Service
 
@Repository
 
@Controller
 
 
 
3. It creates one object (bean) of each class by default (Singleton scope).
 
 
4. Whenever another class needs it, Spring injects the same instance (not a new one).
 
 
 
 
---
 
🏷️ So is @Repository Singleton?
 
✅ Yes — by default, @Repository beans are Singleton.
 
This means:
 
Only one instance of your repository class (like PizzaRepository) exists in the entire application.
 
That instance is reused wherever you use @Autowired to inject it.
 
 
Example:
 
@Repository
public interface PizzaRepository extends JpaRepository<Pizza, Long> {
}
 
Even if you inject this repository into multiple services:
 
@Service
public class OrderService {
    @Autowired
    private PizzaRepository pizzaRepository;
}
 
@Service
public class MenuService {
    @Autowired
    private PizzaRepository pizzaRepository;
}
 
👉 Both OrderService and MenuService will get the same single instance of PizzaRepository.
 
 
---
 
💡 Why Singleton is Useful
 
Saves memory — no need to create multiple objects.
 
Improves performance — one shared bean across the app.
 
Simplifies management — Spring controls the lifecycle of beans.
 
 
 
---
 
🔄 Other Scopes in Spring (for knowledge)
 
Spring supports multiple bean scopes:
 
Scope	Meaning	Example Use
 
singleton	One instance per Spring container (default)	@Repository, @Service
prototype	New instance every time it’s requested	For short-lived objects
request	One instance per HTTP request	In web apps
session	One instance per user session	User-specific data
application	One instance per servlet context	Shared application data
 
 
If you want a bean not to be singleton, you can explicitly declare:
 
@Scope("prototype")
@Component
public class MyBean { ... }
 
 
---

Dependency Injection (DI)
 
DI means that Spring injects objects into classes automatically, rather than you creating them manually.
 
Example:
 
@Service
public class OrderService {
    private final PizzaRepository repo;
 
    @Autowired
    public OrderService(PizzaRepository repo) {
        this.repo = repo;  // Spring injects the singleton repository bean
    }
}
 
This allows loose coupling, testability, and easier maintenance.

5. Logging
 
Use SLF4J logger instead of System.out.println.
 
private static final Logger logger = LoggerFactory.getLogger(PizzaService.class);
logger.info("Fetching all pizzas");

    What is Logging?
Logging is the process of recording information about what your application is doing. It helps developers understand:

What happened in the app
When it happened
Where it happened
If something went wrong

Think of it like a diary or black box for your application—it keeps track of events so you can debug, monitor, and improve your app.

🔍 Why is Logging Important?
Logging helps you:

Debug errors (e.g., why a request failed)
Monitor performance (e.g., how long a task took)
Audit activity (e.g., who logged in and when)
Understand user behavior
Detect security issues


🛠️ Logging in Spring Boot
Spring Boot uses a logging framework called SLF4J (Simple Logging Facade for Java) with Logback as the default implementation.
You can also use other logging frameworks like:

Log4j2
Java Util Logging


📦 How Logging Works in Code
Here’s a simple example using SLF4J in a Spring Boot app:
Java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.*
;@RestController
public class HelloController {    
private static final Logger logger = LoggerFactory.getLogger(HelloController.class);  
@GetMapping("/hello") 
public String sayHello() {   
logger.info("Hello endpoint was called");       
return "Hello, Pavithra!";   
}
}


 Logging Levels
      Logging levels define how important a message is:
 
Level	Purpose
TRACE	 Very detailed info (used for debugging)
DEBUG	 Info useful for debugging
INFO	 General info (e.g., app started)
WARN	 Something unexpected, but not an error
ERROR	 Something went wrong
