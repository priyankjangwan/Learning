# Spring Bean Creation Process (Spring Framework / Spring Boot)

Spring creates and manages objects called **beans** inside an **ApplicationContext**. Below is the typical lifecycle Spring follows to discover, instantiate, configure, proxy, and destroy beans.

---

## 1) Container Bootstrap (Create the `ApplicationContext`)

- Application starts (e.g., `SpringApplication.run(...)` in Spring Boot).
- Spring creates an `ApplicationContext` (examples: `AnnotationConfigApplicationContext`, `ServletWebServerApplicationContext`).

---

## 2) Load / Register Bean Definitions

Spring first builds **BeanDefinitions** (metadata describing how to create beans), usually from:

- `@Configuration` + `@Bean` methods
- Component scanning: `@Component`, `@Service`, `@Repository`, `@Controller`
- Spring Boot auto-configuration
- XML configuration (less common now)

At this stage, Spring mostly has **recipes**, not the actual objects.

---

## 3) BeanFactoryPostProcessor Phase (Modify Definitions Before Objects Exist)

Before instantiating most singleton beans, Spring allows special components to **customize/modify bean definitions**:

- `BeanFactoryPostProcessor`
- `BeanDefinitionRegistryPostProcessor`

Example use cases:
- resolve `${...}` placeholders from properties
- register additional bean definitions programmatically

Think of this as: **change the recipe before cooking**.

---

## 4) Bean Instantiation (Create Non-Lazy Singletons)

By default, Spring creates **singleton** beans eagerly at startup (`lazy=false`).  
For each bean, the common steps are:

### 4.1 Instantiate (Constructor Phase)

- Spring chooses a constructor (commonly the one used for constructor injection).
- Creates the bean instance.

### 4.2 Dependency Injection / Property Population

Spring resolves and injects dependencies via:
- constructor injection
- setter injection
- field injection (not recommended, but supported)

This includes resolving:
- `@Autowired`
- `@Value`
- other beans from the context

### 4.3 Aware Callbacks (Optional)

If the bean implements *Aware* interfaces, Spring calls them, e.g.:

- `BeanNameAware`
- `BeanFactoryAware`
- `ApplicationContextAware`

This provides container-related objects/metadata to the bean.

### 4.4 BeanPostProcessor — *Before Initialization*

Spring calls:
- `BeanPostProcessor#postProcessBeforeInitialization(...)`

Many framework features hook in here.

### 4.5 Initialization Callbacks

Spring runs initialization callbacks such as:

- `@PostConstruct`
- `InitializingBean.afterPropertiesSet()`
- custom init method (`@Bean(initMethod = "init")`)

### 4.6 BeanPostProcessor — *After Initialization* (Proxies Often Created Here)

Spring calls:
- `BeanPostProcessor#postProcessAfterInitialization(...)`

This is where Spring may wrap the bean in a **proxy** to enable features like:

- `@Transactional`
- `@Async`
- `@Cacheable`
- AOP aspects (`@Aspect`)

Important: the bean you inject may be a **proxy**, not the raw class instance.

---

## 5) Bean Ready for Use

After creation and initialization:
- singleton beans are stored in the singleton cache
- the container can inject them into other beans

---

## Bean Scopes (How Often Beans Are Created)

- **Singleton (default):** one instance per `ApplicationContext`
- **Prototype:** a new instance every time the bean is requested
- **Request / Session (web apps):** per HTTP request or session

Notes:
- Spring does **not** fully manage destruction lifecycle for **prototype** beans.

---

## Destruction Phase (When the Context Shuts Down)

On application shutdown/context close, Spring calls destroy callbacks:

- `@PreDestroy`
- `DisposableBean.destroy()`
- custom destroy method (`@Bean(destroyMethod = "cleanup")`)

---

## Circular Dependencies (Important)

- Spring can sometimes resolve circular dependencies with **setter/field injection** using “early references”.
- Circular dependencies with **constructor injection** typically fail (neither bean can be constructed first).

---

## Quick Summary (Lifecycle Order)

1. Create `ApplicationContext`
2. Register BeanDefinitions

