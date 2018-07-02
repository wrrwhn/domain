---
title: "Hello.Spring.JPA"
date: "2016-12-11"
categories:
 - "整理"
tags:
 - "Spring"
 - "JPA"
toc: true
---


# 示例
## Basic Example
```
Entity
@Entity
@Table(name = "user")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @Column(name = "user_name")
    private long userName;
}

Repository
public interface UserRepository extends CrudRepository<User, Long> { // 必须指定操作对象及ID类型

  Long countByLastname(String lastname);
  List<User> findByLastname(String lastname);
}

Common-Repository
PagingAndSortingRepository
CrudRepository
Repository

Config
@Configuration
@EnableJpaRepositories(basePackages = "com.yunkai") // 指定 Repository 位置
@ComponentScan(basePackages = "com.yunkai")
@EntityScan(basePackages = "com.yunkai") // 指定 Entity 对象位置
class ApplicationConfiguration {
  @Bean
  public EntityManagerFactory entityManagerFactory() {
    // …
  }
}

Using
@Autowired
private UserRepository repository;

Page<User> users = repository.findAll(new PageRequest(1, 20));

```

## Cunstom-tuning
```
@NoRepositoryBean // 自定义基础库时使用，保证能被运行时创建
interface MyBaseRepository<T, ID extends Serializable> extends Repository<T, ID> {
  T save(T entity);
}
```


## Defining query methods
```
Query lookup strategies
CREATE 
USE_DECLARED_QUERY 
CREATE_IF_NOT_FOUND = CREATE+ USE_DECLARED_QUERY

Query creation
prefixes
find…by
read…by
query…by
count…by
get…by
expressions
distinct
and
or
operators
between
lessThan
GreaterThan
like
orderBy…asc|desc
parameter handling
Page
Slice
List
Pageable
Sort
limiting
first
top
Streaming
Java 8 Stream<T>
try (Stream<User> stream = repository.findAllByCustomQueryAndStream()) {
  stream.forEach(…);
}

Example
// 获取唯一值
List<Person> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
// 忽略大小写，单独忽略时跟在查询元素后
List<Person> findByLastnameIgnoreCase(String lastname);
List<Person> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname);
// 排序
List<Person> findByLastnameOrderByFirstnameAsc(String lastname);
// 查询 x.address.zipCode ，其中算法会遍历 AddressZipCode/ AddressZip+ Code/ Address+ ZipCode
List<Person> findByAddressZipCode(ZipCode zipCode);
// 同上，明确分词
List<Person> findByAddress_ZipCode(ZipCode zipCode);
// 分页列表查询
Page<User>|Slice<User>|List<User> findByLastname(String lastname, Pageable pageable|Sort sort);
// 指定前X条记录
User find[First|Top]ByOrderByLastnameAsc();
Page<User> query[First10|Top3]ByLastname(String lastname, Pageable pageable);
// 
@Query("select u from User u")
Stream<User> findAllByCustomQueryAndStream();
Stream<User> readAllByFirstnameNotNull();
```


## Data extensions
```
Web support
java config
@Configuration
@EnableWebMvc
@EnableSpringDataWebSupport
class WebConfiguration { }

Xml config
<bean class="org.springframework.data.web.config.SpringDataWebConfiguration" />
<!-- If you're using Spring HATEOAS as well register this one *instead* of the former -->
<bean class="org.springframework.data.web.config.HateoasAwareSpringDataWebConfiguration" />

config support
DomainClassConverter
Define
convert reuqest parameters or path variables to domain class
Example
@RequestMapping("/{id}")
   public String showUserForm(@PathVariable("id") User user, Model model) {} // id-> user，domain call findOne automatic
HandlerMethodArgumentResolver
Define
resovle Pageable/ Sort instances from request parameters
Example
@RequestMapping
   public String showUsers(Model model, 
   Pageable pageable) {}
   public String showUsers(Model model,
        @Qualifier("foo") Pageable first,
          @Qualifier("bar") Pageable second) { … }
    Url request
     page=1&size=10&sort=firstname&sort=lastname,asc
    Attention
     @PageableDefaults on controller.method
Auto create next and prev link
method
@RequestMapping(value = "/persons", method = RequestMethod.GET)
HttpEntity<PagedResources<Person>> persons(Pageable pageable,
PagedResourcesAssembler assembler) {
Page<Person> persons = repository.findAll(pageable);
return new ResponseEntity<>(assembler.toResources(persons), HttpStatus.OK);
}
response
{ 
"links" : [ { "rel" : "next", "href" : "http://localhost:8080/persons?page=1&size=20 }],
"content" : [… // 20 Person instances rendered here],
"pageMetadata" : {
"size" : 20,
"totalElements" : 30,
"totalPages" : 2,
"number" : 0
}
}

Repository populators
Json file
[ { "_class" : "com.acme.Person",
 "firstname" : "Dave",
  "lastname" : "Matthews" },
  { "_class" : "com.acme.Person",
 "firstname" : "Carter",
  "lastname" : "Beauford" } ]
XML config
<repository:jackson-populator locations="classpath:data.json" />
<oxm:jaxb2-marshaller contextPath="com.acme" />

```

## Query methods
```
Query creation
**Keyword** **Sample** **JPQL snippet**
And  findByLastnameAndFirstname … where x.lastname = ?1 and x.firstname = ?2
Or  findByLastnameOrFirstname  … where x.lastname = ?1 or x.firstname = ?2
Is,Equals  findByFirstnameIs  … where x.firstname = 1?
Between  findByStartDateBetween  … where x.startDate between 1? and ?2
LessThan  findByAgeLessThan  … where x.age < ?1
LessThanEqual findByAgeLessThanEqual  … where x.age ⇐ ?1
GreaterThan  findByAgeGreaterThan  … where x.age > ?1
GreaterThanEqual  findByAgeGreaterThanEqual  … where x.age >= ?1
After findByStartDateAfter  … where x.startDate > ?1
Before findByStartDateBefore  … where x.startDate < ?1
IsNull  findByAgeIsNull  … where x.age is null
IsNotNull,NotNull findByAge(Is)NotNull  … where x.age not null
Like  findByFirstnameLike  … where x.firstname like ?1
NotLike  findByFirstnameNotLike  … where x.firstname not like ?1
StartingWith  findByFirstnameStartingWith  … where x.firstname like ?1 (parameter bound with appended %)
EndingWith findByFirstnameEndingWith  … where x.firstname like ?1 (parameter bound with prepended %)
Containing  findByFirstnameContaining  … where x.firstname like ?1 (parameter bound wrapped in %)
OrderBy  findByAgeOrderByLastnameDesc  … where x.age = ?1 order by x.lastname desc
Not  findByLastnameNot  … where x.lastname <> ?1
In  findByAgeIn(Collection<Age> ages)  … where x.age in ?1
NotIn findByAgeNotIn(Collection<Age> age) … where x.age not in ?1
True  findByActiveTrue() … where x.active = true
False  findByActiveFalse()  … where x.active = false
IgnoreCase  findByFirstnameIgnoreCase  … where UPPER(x.firstame) = UPPER(?1)

Name Query
XML
/META-INF/orm.xml
<named-query name="User.findByLastname">
  <query>select u from User u where u.lastname = ?1</query>
</named-query>
Annotation
@Entity
@NamedQuery(name = "User.findByEmailAddress",
  query = "select u from User u where u.emailAddress = ?1")
public class User {}
Repository
public interface UserRepository extends JpaRepository<User, Long> {
  User findByEmailAddress(String emailAddress);
}

Query
take precedence over name query
Annotation
JPQL query 
@Query("select u from User u where u.firstname like %?1")
List<User> findByFirstnameEndsWith(String firstname);
Native Query
@Query(value = "SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?0", nativeQuery = true)
User findByEmailAddress(String emailAddress);
@Query("select u from #{#entityName} u where u.lastname = ?1")
List<User> findByLastname(String lastname);
Native Query with named parameters
@Query("select u from User u where u.firstname = :firstname or u.lastname = :lastname")
User findByLastnameOrFirstname(@Param("lastname") String lastname, @Param("firstname") String firstname);

Modifying Query
Annotation
@Modifying
@Query("update User u set u.firstname = ?1 where u.lastname = ?2")
int setFixedFirstnameFor(String firstname, String lastname);
注： @Modifying(clearAutomatically = true) 可促使 EntityManager 立即抛弃缓存的数据并重新读取

Query Hints
user for cache data
Annotation
@QueryHints({ @QueryHint(name = "org.hibernate.cacheable", value ="true") })
public User findById(long id);

Load Graphs
Annotation
@Entity
@NamedEntityGraph(name = "GroupInfo.detail", attributeNodes = @NamedAttributeNode("members"))
public class GroupInfo {}

@Repository
public interface GroupRepository extends CrudRepository<GroupInfo, String> {
   @EntityGraph(value = "GroupInfo.detail", type = EntityGraphType.LOAD)
   GroupInfo getByGroupName(String name);
}
```

## Stored Procedures
```
HSQL DB Definition
/;
DROP procedure IF EXISTS plus1inout
/;
CREATE procedure plus1inout (IN arg int, OUT res int)
BEGIN ATOMIC
set res = arg ` 1;
END
/;
Entity Definition
@Entity
@NamedStoredProcedureQuery(name = "User.plus1", procedureName = "plus1inout", parameters = {
@StoredProcedureParameter(mode = ParameterMode.IN, name = "arg", type = Integer.class),
@StoredProcedureParameter(mode = ParameterMode.OUT, name = "res", type = Integer.class) })
public class User {}
Mapped In Database
@Procedure("plus1inout")
Integer explicitlyNamedPlus1inout(Integer arg);
```


## Specifications
```
Interface
public interface Specification<T> {
  Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder builder);
}
Definition
public class CustomerSpecs {
  public static Specification<Customer> isLongTermCustomer() {
    return new Specification<Customer>() {
      public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder builder) {
         LocalDate date = new LocalDate().minusYears(2);
         return builder.lessThan(root.get(_Customer.createdAt), date);
      }
    };
  }
  public static Specification<Customer> hasSalesOfMoreThan(MontaryAmount value) {
    return new Specification<Customer>() {
      public Predicate toPredicate(Root<T> root, CriteriaQuery<?> query, CriteriaBuilder builder) {}
    };
  }
}
Using
List<Customer> customers = customerRepository.findAll(where(isLongTermCustomer()).or(hasSalesOfMoreThan(amount)));

```


## Transaction
```
Retrieve
Benefit
rollback after setted timeout
improve the 
Example
@Transactional(timeout = 10, readOnly= true)
public List<User> findAll();

Create/Update/Delete
Benefit
ensure the data is correct
Example
@Transactional
public void addRoleToAllUsers(String roleName)
@Modifying
@Transactional
@Query("delete from User u where u.active = false")
void deleteInactiveUsers();
```


## Locking
```
@Lock(LockModeType.READ)
List<User> findByLastname(String lastname);
```


## Auditing
```
Base Metadata Annotation
@CreateBy | @LastModifiedBy | @CreateDate | @LastModifiedDate
-> 实体调整时自动赋值

```



# 参考
[Spring Data JPA - Reference Documentation](http://docs.spring.io/spring-data/jpa/docs/1.8.0.RELEASE/reference/html/)