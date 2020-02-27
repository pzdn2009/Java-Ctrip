# JPA

JPA\(Java Persistence API\)。主要是为了简化现有的持久化开发工作和整合ORM技术。JPA是一套规范，不是一套产品。

## 1. 引用

教程Ref：[https://spring.io/guides/gs/accessing-data-jpa/](https://spring.io/guides/gs/accessing-data-jpa/)

官方文檔Ref：[http://docs.spring.io/spring-data/data-jpa/docs/current/reference/html/](http://docs.spring.io/spring-data/data-jpa/docs/current/reference/html/)

中文版指南：[https://ityouknow.gitbooks.io/spring-data-jpa-reference-documentation/content/](https://ityouknow.gitbooks.io/spring-data-jpa-reference-documentation/content/)

### 3.2 Repository的註解

* @Query\(value = "select \* from t\_userinfo limit ?1", nativeQuery =true\)
* @Transactional

## 4. Repository

Spring Data JPA creates an implementation on the fly when you run the application.

* Repository
* CrudRepository
* PagingAndSortingRepository

### 4.1 CrudRepository

```java
package hello;
import java.util.List;
import org.springframework.data.repository.CrudRepository;

public interface CustomerRepository extends CrudRepository<Customer, Long> {
    List<Customer> findByLastName(String lastName);
}
```

默認方法：

```java
userRepository.findAll();
userRepository.findOne(1l);
userRepository.save(user);
userRepository.delete(user);
userRepository.count();
userRepository.exists(1l);
```

自定義簡單查詢：

findXXBy,readAXXBy,queryXXBy,countXXBy, getXXBy。

複雜查詢：

```java
Page<User> findByUserName(String userName,Pageable pageable);

@Test
public void testPageQuery() throws Exception {
    int page=1,size=10;
    Sort sort = new Sort(Direction.DESC, "id");
    Pageable pageable = new PageRequest(page, size, sort);
    userRepository.findALL(pageable);
    userRepository.findByUserName("testName", pageable);
}

@Transactional
@Modifying
@Query("delete from User where id = ?1")
void deleteByUserId(Long id);
```

### 4.2 PagingAndSortingRepository

PagingAndSortingRepository 接口继承于 CrudRepository 接口，拥有CrudRepository 接口的所有方法， 并新增两个方法：分页和排序。 但是**这两个方法不能包含筛选条件**。

```java
@NoRepositoryBean
public interface PagingAndSortingRepository<T, ID extends Serializable> extends CrudRepository<T, ID> {

    /**
     * Returns all entities sorted by the given options.
     * 
     * @param sort
     * @return all entities sorted by the given options
     */
    Iterable<T> findAll(Sort sort);

    /**
     * Returns a {@link Page} of entities meeting the paging restriction provided in the {@code Pageable} object.
     * 
     * @param pageable
     * @return a page of entities
     */
    Page<T> findAll(Pageable pageable);
}
```

Sample：

```java
@Test  
    public void test_findAll_page(){  
        int currentPage =0; //当前页从0 开始  
        int pageSize = 5;  

        //排序  
        Order idOrder = new Order(Direction.DESC, "id");  
        Order nameOrder = new Order(Direction.ASC,"name");  
        Sort sort = new Sort(idOrder,nameOrder);  
        PageRequest pageRequest  = new PageRequest(currentPage, pageSize, sort);  

        Page<StudentPO> page = this.studentdPageSortRepository.findAll(pageRequest);  
        System.out.println("总记录数:" + page.getTotalElements());  
        System.out.println("总页数:" + page.getTotalPages());  
        System.out.println("当前页（request):" + page.getNumber());  
        System.out.println("当前页总记录数（request):" + page.getSize());  
        System.out.println("当前页记录总数：" + page.getNumberOfElements());  
        List<StudentPO> students = page.getContent();  
        for (StudentPO studentPO : students) {  
            System.out.println(studentPO);  
        }  
    }
```

* 排序語法構造：Sort sort = new Sort\(Direction.DESC, "sort"\).and\(new Sort\(Direction.DESC, "id"\)\);

## 5. Test

```java
    @Bean
    public CommandLineRunner demo(CustomerRepository repository) {
        return (args) -> {
            // save a couple of customers
            repository.save(new Customer("Jack", "Bauer"));
            repository.save(new Customer("Chloe", "O'Brian"));
            repository.save(new Customer("Kim", "Bauer"));
            repository.save(new Customer("David", "Palmer"));
            repository.save(new Customer("Michelle", "Dessler"));

            // fetch all customers
            log.info("Customers found with findAll():");
            log.info("-------------------------------");
            for (Customer customer : repository.findAll()) {
                log.info(customer.toString());
            }
            log.info("");

            // fetch an individual customer by ID
            Customer customer = repository.findOne(1L);
            log.info("Customer found with findOne(1L):");
            log.info("--------------------------------");
            log.info(customer.toString());
            log.info("");

            // fetch customers by last name
            log.info("Customer found with findByLastName('Bauer'):");
            log.info("--------------------------------------------");
            for (Customer bauer : repository.findByLastName("Bauer")) {
                log.info(bauer.toString());
            }
            log.info("");
        };
    }
```

## 6. 坑

### 6.1 MS SQL Exception: Incorrect syntax near '@P0'

解決：MS SQL TOP需要加上括號

```sql
SELECT TOP (?)
```

使用2008的方言：

```text
spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.SQLServer2008Dialect
```

Ref：Upgraded hibernate to version 5.x and came across this issue. Had to update "hibernate.dialect" configuration from org.hibernate.dialect.SQLServerDialect to org.hibernate.dialect.SQLServer2012Dialect.

### 6.2 映射NVARCHAR

NVARCHAR\(MAX\)使用@Lob來完成映射

### 6.3 映射UNIQUEIDENTIFIER

用類型UUID不行，改為String即可。

```java
@Id
@GenericGenerator(name = "generator", strategy = "guid", parameters = {})
@GeneratedValue(generator = "generator")
@Column(name = "APPLICATION_ID" , columnDefinition="uniqueidentifier")
private String id = UUID.randomUUID().toString();
```

### 6.4 hb5.0命名策略

Ref：[https://github.com/hibernate/hibernate-orm/blob/5.0/migration-guide.adoc\#naming-strategies](https://github.com/hibernate/hibernate-orm/blob/5.0/migration-guide.adoc#naming-strategies)

```text
# 有 ImprovedNamingStrategy的效果。
org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy

# 有 DefaultNamingStrategy的效果。
org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
```

### 6.5 多條件查詢與分頁

spring Data JPA支持JPA2.0的Criteria查询，相应的接口是JpaSpecificationExecutor。

**Criteria 查询**：是一种类型安全和更面向对象的查询 。

```java
private class MySpec implements Specification<ResourceItem> {

    private GetResourceItemListRequestDTO requestDTO;

    public MySpec(GetResourceItemListRequestDTO requestDTO) {
        this.requestDTO = requestDTO;
    }

    @Override
    public Predicate toPredicate(Root<ResourceItem> root, CriteriaQuery<?> query, CriteriaBuilder cb) {

        List<Predicate> list = new ArrayList<Predicate>();

        list.add(cb.equal(root.get("applicationID"), requestDTO.getApplicationId()));
        list.add(cb.equal(root.get("categoryID"), requestDTO.getCategoryId()));

        Predicate[] p = new Predicate[list.size()];

        return cb.and(list.toArray(p));
    }
}

var query = repository.findAll(new MySpec(requestDTO),new PageRequest(requestDTO.getPage(), requestDTO.getSize()));
```

手殘的：PageRequest的page與size傳遞反了。

### 6.6 映射tinyint

对应java的Byte类型。

## 7. JPA中使用Convertor

AttributeConverter

* 持久化enum
* 加解密数据
* 持久化日期

轉換枚舉值：

```java
@Convert(converter = TransactionOperation.Converter.class)
private TransactionOperation operationType;

public enum TransactionOperation {
    PAY(1, "收款"),
    AUTHORIZE(3, "預授權"),

        @Getter
    private int code;

    @Getter
    private String chineseName;

    TransactionOperation(int code, String name_ch) {
        this.code = code;
        this.chineseName = name_ch;
    }

    @javax.persistence.Converter(autoApply = true)
    public static class Converter implements AttributeConverter<TransactionOperation, String> {
        @Override
        public String convertToDatabaseColumn(TransactionOperation transactionOperation) {
            return transactionOperation.name();
        }

        @Override
        public TransactionOperation convertToEntityAttribute(String s) {
            return TransactionOperation.valueOf(s);
        }
    }
}
```

JPA中的日期轉換：

```java
import javax.persistence.AttributeConverter;
import javax.persistence.Converter;
import java.sql.Timestamp;
import java.time.LocalDateTime;

@Converter(autoApply = true)
public class LocalDateTimeAttributeConverter implements AttributeConverter<LocalDateTime, Timestamp> {

    @Override
    public Timestamp convertToDatabaseColumn(LocalDateTime locDateTime) {
        return (locDateTime == null ? null : Timestamp.valueOf(locDateTime));
    }

    @Override
    public LocalDateTime convertToEntityAttribute(Timestamp sqlTimestamp) {
        return (sqlTimestamp == null ? null : sqlTimestamp.toLocalDateTime());
    }
}
```

