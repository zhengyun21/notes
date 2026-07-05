1. 核心注解
	- **@Component**: 这是一个通用注解，它将 Java 类标记为 Spring 的组件。这意味着 Spring 容器可以自动检测和配置这些类。
    
	- **@Service**: 特化的 @Component 注解，它用于标记服务层的组件。
    
	- **@Repository**: 特化的 @Component 注解，用于标记数据访问层（DAO层）的组件。
    
	- **@Controller**: 特化的 @Component 注解，用于标记控制层的组件，如 Spring MVC 控制器。
    
	- **@Autowired**: 自动注入依赖项。Spring 容器会在创建 bean 时自动注入标记有此注解的依赖项。
    
	- **@Qualifier**: 与 @Autowired 注解一起使用，当有多个 bean 候选者需要自动注入同一个依赖时，用于指定注入哪一个 bean。
    
	- **@Primary**: 当有多个同类型的 bean 候选时，被注解为 @Primary 的 bean 会被优先考虑。
    
	- **@Configuration**: 表示一个类声明了一个或多个 @Bean 方法，并且可能会被 Spring 容器用来生成 bean 定义和服务请求。
    
	- **@Bean**: 用在方法上，表明该方法产生一个由 Spring 容器管理的 bean。
    
	- **@Scope**: 定义 bean 的作用域，如单例（singleton）、原型（prototype）、请求（request）、会话（session）等。
    
	- **@Value**: 用于注入属性值，可以是硬编码的值，也可以是来自配置文件的值。
    

2. Spring MVC 注解

	- **@RequestMapping**: 用于将 HTTP 请求映射到特定的处理方法上。
    
	- **@GetMapping, @PostMapping, @PutMapping, @DeleteMapping**: 是 @RequestMapping 的特化版本，分别用于处理 HTTP 的 GET, POST, PUT, DELETE 请求。
    
	- **@PathVariable**: 用于将 URL 中的模板变量映射到方法的参数上。
    
	- **@RequestParam**: 用于将请求参数映射到方法的参数上。
    
	- **@RequestBody**: 用于将 HTTP 请求体中的 JSON 或 XML 数据绑定到方法的参数上。
    
	- **@ResponseBody**: 表明方法的返回值应该被直接写入 HTTP 响应体中，通常用于异步请求。
    
	- **@RestController**: 是 @Controller 和 @ResponseBody 的组合注解，用于创建 RESTful 控制器。
		
	- **RestControllerAdvice**：全局异常处理器

3. Spring Boot 注解

	- **@SpringBootApplication**: 是一个方便的注解，它包含了 @Configuration, @EnableAutoConfiguration, 和 @ComponentScan。它通常用于主类上，以启动 Spring Boot 应用程序。
    
	- **@EnableAutoConfiguration**: 告诉 Spring Boot 根据类路径设置、其他 bean 和各种属性设置开始添加 bean。
    
	- **@EnableAspectJAutoProxy**: 启用对 AspectJ 自动代理的支持，允许使用 @Aspect 注解定义切面。
    

4. 数据访问和事务注解

	- **@Transactional**: 声明事务的边界。默认情况下，如果在方法执行过程中发生运行时异常，事务将回滚。
    
	- **@EnableJpaRepositories**: 启用 JPA 仓库。如果你使用 Spring Data JPA，这个注解会自动配置你的仓库。
    

5. 测试相关注解

	- **@RunWith**: 用于定义测试运行器，Spring 中通常用于对 JUnit 的支持。
    
	- **@SpringBootTest**: 用于提供 Spring Boot 应用程序测试的配置。
    

6. 其他注解

	- **@Profile**: 指定在不同环境下，某些组件是否应该被注册到 Spring 容器中。
    
	- **@PropertySource**: 用于指定一个属性文件，Spring 容器会从该文件中加载属性。
