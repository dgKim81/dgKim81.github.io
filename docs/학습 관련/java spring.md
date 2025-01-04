spring 스탭샷은 쓰지 마라.
스냅샷 버젼은 spring boot team이 개발하고 있다.
새로운 것을 배울 때 사용할 버젼이 아닙니다.

그룹ID 아티팩트ID
패키지 이름 및 클래스 이름과 유사하다.

start.spring.io 에서 프로젝트를 생성한다.

소프트웨어 기술에서 유일하게 변하지 않는 것은 변화 입니다.

spring에서 관리하는 것들을 spring bean 이라고 한다.

record Person(String name, int age) {}; // jdk 16에 추가되었다.

getBean 검색에 여러가지 방식을 제공한다.

Bean의 이름은 사용자 @Bean에서 사용자 정의가 가능하고 기본은 메서드 명이다.
spring context에서 bean을 검색가능하다.
bean을 사용해서 새로운 bean을 생성가능하다.
bean 검색할때 여러개가 나오면 문제가 된다.

spring container -> IOC container
Bean Factory : Basic Spring Contaier -> IOT 메모리에 심한 제약
Application Context : Advenced Apring containser with enterprise-specific faeatures -> IOC Container
*. 일반적으로 사용한다.

POJO : 오래된 자바.. 클래스
Java Bean : EJB 오느날 많이 쓰지 않는다. 별로 중요하지 않다.
제약 조건
// public no-arg constructor
// getter and setter 
// implements Serializable
Spring Bean : Spring에서 관리하는 Bean이다.

spring.getBeanDefinitionCount
@Primary 옵션으로 여러개의 빈에서 우선순위를 줄 수 있다.
@Qualifier("address3Qualifier") 객체 한정자를 외부에서 사용할 수 있다.

@Component를 붙이면 스프링 빈에서 
위치를 알려줘야 한다. 
@ComponentScan("com.in28minutes.learn_spring_framework.game") // 위치를 알려주는 코드이다.

Autowired가 없어도.. 자동으로 처리한다.

@Primary: 여러 후보중  @primary에 우선권을 준다.

@Qualifier : 선택적으로 AutoWired를 수행할 수 있다.
@Autowired @Qualifier(...) 같이 쓴다.
@Qualifier("superContraGameQualifier")
public class SuperContraGame implements GamingConsole {
    ...
public GameRunner(@Qualifier("superContraGameQualifier") GamingConsole game) {
    this.game = game;
}

Qualifier가 지정되어있지 않다면, Qualifier 다음에 redixSort 클래스명을 붙인다.
이때 앞글자는 소문자이다!!

의존성 주입 종류(생성자 주입을 추천한다.)
1. 생성자. @Autowired도 필요 없다...
    public YourBusinessClass(Dependency1 dependency1, Dependency2 dependency2) {
        this.dependency1 = dependency1;
        this.dependency2 = dependency2;
    }

2. 수정자 기반.(setter) setter 메서드에다가 @Autowired를 쓴다.
    @Autowired
    public void setDependency1(Dependency1 dependency1) {
        this.dependency1 = dependency1;
    }
3. 필드 기반 @Autowired - 필드에 정의해준다. 

스프링 프레임워크의 중요한 용어
@component : 검색되면 스프링 빈으로 사용할 수 있는 인스턴스의 클래스
Dependency : 
@ComponentScan : 컴포넌트를 검색할 패키지 스캔
DependencyInjection: 의존성 주입
IOC - Inversion Of Control
IOC Container - ApplicationContext(복잡함), BeanFactory(간단함, 거의 안씀)
@Autowiring : 스프링 빈의 의존성을 연결하는 처리

@Coponent와 @Bean의 비교.
@Component : 대부분의 상황에서 사용된다.
@Bean : 3자 라이브러리 생성 및 비즈니스 모델 작성시


@Lazy : 사용하기 직전에 로드 한다., 권장되지 않고 자주 사용되지 않는다.
즉시 초기화를 권장한다.

Configuration에서도 사용이 가능하다. -> bean 지연초기화

지연초기화를 사용하는 경우 스프링 초기 시작에서 구성 오류를 발견하지 못한다.

@Scope
default는 한번 생성하면 같은 인스턴스가 반환된다. -> single ton
scope prototype는 생성할 때마다 다른 인스턴스가 생성된다.
scope webapplication 
    scope request 웹 요청별 하나
    scope session 웹 세션별 하나
    scope application 웹 어플리케이션 전체에서 하나
    scope websocket 웹소겟 인스턴스 하나당

싱클턴 - 스프링은 약간 다르다. 
 하나의 ioc container에서 하나의 오브젝트 이다.
 자바 싱글톤은 jvm에 하나이다... 이건 어떻게 보면 당연한 얘기인데 ''?

spring scope prototype은 bean이 상태를 가지는 경우.
spring scope singleton은 bean이 상태를 가지지 않는 경우.

jakarta.annotation.PostConstruct 
스프링 빈이 생성된 후에 초기화 로직을 넣을 수 있다.
jakarta.annotation.PreDestroy
스프링 빈이 파괴되기 전에 정리 로직을 넣을 수 있다.

규격 그룹 이다.
j2ee java 2 platform enterprise edition
java ee - java 2 platform enterprise edition 5 ~ 8
jakarta ee 
    important Specifications:
        Jakarta Server Pages JSP
        Jakarta Standard Tag Library JSTL
        Jakarta Enterprise Beans EJB
        Jakarta RESTFul Web Services JAX-RS
        Jakarta Bean Validation
        Jakarta Context and Dependency Injection CDI
        Jakarta Persistence JPA
    Supperoted by Spring 6 and Spring Boot 3
        javax. 대신에 jakarta. 패키지를 사용할 것 이다.

Jakarta 중요한 주입 api 어노테이션
Inject (spring Autowired)
Named (spring Component)
Qualifier
Scope
Singleton

AnnotationConfigApplicationContext 사용해왔는데..

try (var context = new ClassPathXmlApplicationContext("contextConfiguration.xml")) 
XML 설정을 사용하겠다.
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"> <!-- bean definitions here -->
	
    <bean id="name" class="java.lang.String">
        <constructor-arg value="Ranga"/>
    </bean>
 	
    <bean id="age" class="java.lang.Integer">
        <constructor-arg value="12"/>
    </bean>

    <!-- <context:component-scan base-package="com.in28minutes.learn_spring_framework.game"></context:component-scan> -->
    <bean id="pacmanGame" class="com.in28minutes.learn_spring_framework.game.PacmanGame">
    </bean>

    <bean id="gameRunner" class="com.in28minutes.learn_spring_framework.game.GameRunner">
        <constructor-arg ref="pacmanGame"></constructor-arg>
    </bean>
</beans>

spring stereotype
@Component 기본 어노테이션 -
    스페셜
    @Service - 비즈니스 로직을 가지고 있는 것을 가리킨다.
    @Controller - 웹 컨트롤러
        - REST API
    @Repository - 데이터베이스와 통신해서 데이터를 저장 검색 조작하는 경우.

최대한 구체적인것을 사용한다.

복습
@Configuration : @Bean 메서드를 하나이상 나타난다.
@ComponentScan : 검포넌트 스캔 위치를 정의한다.
@Bean
@Component : 
@Service :
@Controller :
@Repository :
@Primary : 
@Qualifier : 
@Lazy : 지연 초기화
@Scope : 프로토타입, 싱글턴, request, session
@PostConstruct
@PreDestory : 파괴되기전에 리소스 해제
@Named : Jakarta spring은 이것을 구현하고 있다.
@Inject : jakarta Context 

Dependency Injection
Constr. injection
Setter injection
Field injection
IOC Container
Bean Factory 
Application Context
Spring Bean
Auto-wiring

스프링 부트~!! spring boot
spring boot를 설치해보자..
https://start.spring.io/ 에서

maven, java, spring boot 3.4.1
project metadata 설정
dependencies 설정.

간단한 REST API
@RestController
@RequestMapping

Spring Boot Starter Project가 편리한 의존성 디스크립터라는 것입니다.
Auto Configuration
    class path에 프레임워크 존재하면 자동 설정한다.
    - springboot web 설치시 자동 오류 페이지 
    pom.xml에 starter로 설정이 가능하지만
    기존 있는 것으로 오버라이드 가능하다. (어노테이션이나 관련 설정들...)

application.properties 에 아래와 같이 입력하면 스프링 자동 설정에 대해서 정보가 나타난다.
logging.level.org.springframework=debug

spring boot devTool 서버를 자동으로 재시작 한다.
pom.xml은 수동으로 중지했다가 다시 시작해야 한다.

프로덕션 환경에 배포 준비하기.
Profiles이다.
Dev, QA, Stage, Prod, ...
각 환경마다 다른 설정이 필요하다.
환경별 설정을 제공할 수 있다.
하이픈이 중요한다.
application-dev.properties
application-prod.properties

application.properties는 기본 설정이 된다.

spring.profiles.active=prod 라고 설정할 수 있고
중복되는 설정은 지정된 prod가 우선순위가 높다.

로그레벨은 몇가지가 있다.
trace
debug
info
warning
error
off

복잡한 설정의 예
// currency-service.url=
// currency-service.username=
// currency-service.key=
@ConfigurationProperties(prefix = "currency-service")
@Component
public class CurrencyServiceonfiguration {
    private String url;
    private String username;
    private String key;

spring boot embedded Server
이전의 배포 프로세스
1. WAR Approach(OLD)
 - Install JAVA
 - Install Web/ Application Server
 - Deploy the Application WAR

2. Embedded Server Simpler alternative
 - Install JAVA
 - Run JAR File
*. 톰캣을 JAR 파일 안에 이미 가지고 있다.
*. 기본은 톰캣이지만 spring-boot starter에 다른 서버도 정의되어 있다.

spring boot monitoring
spring boot Actuator
몇 개의 endpoint를 제공하고 있다.
 - beans - 프로젝트의 spring bean의 완전한 목록을 제공한다.
 - health - 어플리케이션의 health information을 제공한다.
 - metrics - 어플리케이션의 merics 정보를 제공한다.
 - mappings - 요청 매핑에 관련 세부 사항을 제공한다.

pom.xml에 아래의 항목을 추가한다.
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

/actuator/ 하면 링크를 알려준다.

더 많은 기능을 사용하고 싶으면 application.properties에 추가 엔드포인트를 설정해야 한다.
management.endpoints.web.exposure.inclued=* 
이렇게 설정하면 모든 endpoint가 노출 된다.
주요 endpoint 
- beans : beans에 대한 세부 정보
- configprops : 설정에 대한 세부 정보
- env : 환경에 대한 세부 정보
- metrics : metrics 목록을 제공한다.

메트릭에대한 정보를 설정하면, CPU와 메모리를 많이 소모한다.
management.endpoints.web.exposure.include=health,metrics

복습
 - Starter Project
 - Auto configuration
 - Actuator
 - DevTools
 - 스프링부트의 장점 설명.


Spring JPA Hibernate
  h2 데이터베이스에 대해서 콘솔을 사용할 수 있도록 한다.
  spring.h2.console.enabled=true 
  http://localhost:8080/h2-console

  아래와 같이 쓰면, url이 고정된다.
  spring.datasource.url=jdbc:h2:mem:testdb

  resources/schema.sql 에 스키마를 정의하면 매번 스키마 파일을 가져와서 사용할 수 있도록 해준다.

spring JDBC 사용.
  
*. """ 이렇게 문자열을 쓰면 줄바꿈도 넣을 수 있다.(텍스트 블록)
CommandLineRunner는 spring application에 포함된 채로 실행 될 때, 함께 실행해준다.
import org.springframework.boot.CommandLineRunner;

public class CourseJdbcCommandLineRunner implements CommandLineRunner {

    @Override
    public void run(String... args) throws Exception {
        
        throw new UnsupportedOperationException("Unimplemented method 'run'");
    }
    
}

JdbcTemplate 템플릿을 사용한다. 
mybatis도 옵션이겠지...

@Entity
@Id
@Column

CourseJpaRepository
EntityManager 객체를 PersistenceContext으로 가져온다. (Autowired 보다 더 구체적인 어노테이션)
@PersistenceContext 컨테이너 관리형 EntityManager 및 그에 연결된 영속성 컨텍스트와의 종속성을 나타낸다.
@Transactional 을 추가해야 한다.

application.properties
spring.jpa.show-sql=true 는 sql을 확인할 수 있다.

spring data jpa사용에 대해서..
jpa를 더 간단하게 만들고, EntityManager도 신경 쓸 필요가 없다.

public interface CourseSpringDataJpaRepository extends JpaRepository<Course, Long>
이렇게 선언한다.

@Component
public class CourseJpaCommandLineRunner {// implements CommandLineRunner {

    @Autowired
    private CourseSpringDataJpaRepository repository;

   //@Override
    public void run(String... args) throws Exception {
        repository.save(new Course(1, "Learn AWS Now!", "in28minutes"));
        repository.save(new Course(2, "Learn Azure", "in28minutes"));
        repository.save(new Course(3, "Learn DevOps", "in28minutes"));

        repository.deleteById(1l);

        System.out.println(repository.findById(2l));
        System.out.println(repository.findById(3l));
    }
    
}

save, findById, deleteById가 자동으로 생성된다.
custom 메서드를 추가할 수 있다.
명명 규칙을 따라야 한다.
List<Course> findByAuthor(String author);

Hibernate와 JPA의 차이
JPA는 기술 명세를 정의 API이다. 인터페이스이다.
Hibernate는 JPA의 인기있는 구현체이다.

Entity어노테이션은 Jakarta에도 있고, Hibernate에도 있지만,
Jakarta 것을 쓴다. 이유는 구현체를 변경할 수도 있기에 인터페이스를 쓰는 것과 같다.

# sprgin boot로 웹어플리케이션 만들기..
jsp setting 하려면
application.properties 에 아래를 추가한다.

\#전체 경로를 기술해야 하지만 해당 경로만 기술할 수 있도록 전치를 붙인다.
spring.mvc.view.prefix=
\#.jsp는 늘 따라다니는 것이므로 붙인다.
spring.mvc.view.suffix=
\#spring의 상세 로그를 볼수 있다.
\#logging.level 하위로 패키지 일부를 쓰면 된다. 아래는  org.springframework 하위를 모두 볼것이므로..
logging.level.org.springframework=debug