# Spring

**Persistance Layer

The persistence layer interacts with relational database and the service layer. It gets data from service layer, performs operations on database and sends back results to service layer. The code to interact with database is implemented in this layer.
Spring is a popular framework for enterprise application development. For implementing persistence layer, it provides many approaches. The developer can choose appropriate Spring modules such as Spring JDBC or Spring ORM to integrate with JPA, Hibernate etc. for implementing the data access layer of an enterprise application.

Spring further simplifies development of persistence layer using Spring Data, which is an umbrella project supporting both relational and non-relational databases effectively. It provides a consistent persistent layer code with minimal coding from the developer thereby eliminating a lot of developer effort.

In this course, you will learn how to develop persistence layer of an application using 
Spring ORM
Spring Data
Spring JDBC 

**Object-Relational Impedance Mismatch

You already know how to use JDBC for developing persistence layer of an application. The JDBC code involves both Java objects (object model) and SQL queries (relational model). This makes it difficult to use because object model needs to be converted to relational model and vice versa. For example, for executing SQL select query the values present in Java object needs to be copied into SQL query but this conversion is not easy because of paradigm mismatch between object and relational model. This paradigm mismatch is called as Object-Relational Impedance Mismatch and it exposes following problems :
1. Problem of Granularity
This problem is that in object model, data can be present in more than one object but in relational model it is present in one table, i.e the object model is more granular than the relational model. For example, in object model you can have two separate classes, one for customer and the other for his address but in relational model, data about customer and his address can be stored in single table as shown below:
2. Problem of Inheritance
This problem is that in object model, you can create classes having inheritance relationship but in relational model there is nothing that can define inheritance.
3. Problem of Identity
This problem is that the way in which equality of two objects is defined in object model is different for the way equality of two rows of a table is defined in relational model. For example, two rows in a table are same if they have same primary key values but two Java objects are equal if equals() method returns true.
4. Problem of Associations
This problem is that in object model, associations between objects is represented using references but in relational model, associations between tables is represented by foreign keys. Also, associations between objects can be unidirectional or bidirectional. However, in associations between tables there is no way to store information about directionality of relationship.
5. Problem of Data Navigation
This problem is that the way you access data in object model is different from the way you do it in a relational database. For example, in Java you navigate across association relationships using dot(.) operator but in relational model you navigate across related tables using joins.

To handle these problems, a technique called as Object-Relational Mapping (ORM) was introduced. It handles object relational impedance mismatch by providing a way to map Java objects to tables so that object model can be automatically translated to relation model and vice versa, allowing developers to focus only on the object model.

Object Relational Mapping (ORM) is a technique or design pattern, which maps an object model with the relational model. It has the following features:
It resolves object-relational impedance mismatch by mapping : 
- Java classes to tables in the database
- Instance variables to columns of a table
- Objects to rows in the table
It helps the developer to get rid of SQL queries so that they can concentrate on the business logic which leads to faster development of the application.
It is database independent. All database vendors provide support for ORM. Hence, the application becomes portable without worrying about the underlying database.
To use ORM in Java applications, Java Persistence API (JPA) specification is used. It has many implementations such as Hibernate, OpenJPA, TopLink, EclipseLink, etc.

**JAVA PERSISTANCE API

The Java Persistence API (JPA) is a specification that defines standard for using object relational mapping (ORM) in Java applications for interacting with relational database. It provides :
- API to map classes with tables
- API for performing CRUD operations
- Java Persistence Query Language (JPQL), a querying language for fetching data from database
- Criteria API which uses object graph to fetch data from database
There are multiple providers available in market which provides implementation of JPA specification such as EclipseLink, OpenJPA, Hibernate, etc.

The classes which are mapped to a table and whose instance represents a row in table are entity classes. JPA provides annotations to create entity classes which are present in the javax.persistence package. Some of these annotations are as follows:

**@Entity : It specifies that the Java class is an entity class. 

@Entity
public class Customer {
 //rest of the code  
}
**@Id : Every object of entity class must have an attribute which uniquely identifies it. This is called as primary key attribute or identifier. Usually, it is the attribute mapped to the primary key column of table. It is specified using @Id annotation.

@Entity
public class Customer {
    @Id
    private Integer customerId;
	//rest of the code
}
**@Table : It specifies the table with which entity class is mapped. By default, entity class is mapped with a table which has same name as the class name. If entity class has to be mapped with a table whose name is different from class name then, this annotation is used. For example, if CustomerDetails class has to be mapped with customer table then the annotation needs to be used as follows:

@Entity
@Table(name="customer")
public class CustomerDetails {
    @Id
    private Integer customerId;
	//rest of the code
}
**@Column : It specifies the name of column in table with which attribute in entity class is mapped. By default, column name is mapped with a column which has same name as the attribute name. If attribute name is different from column name, then this annotation is used. For example, if customerId is to be mapped to customer_id column, then the annotation needs to be used as follows:

@Entity
@Table(name="customer")
public class Customer {
    @Id
    @Column(name="customer_id")
    private Integer customerId;
	//rest of the code
}
**@Transient : It specifies the attributes which are not stored in table.

@Entity
public class Customer {
    @Id
    @Column(name="customer_id")
    private Integer customerId;
    @Column(name="email_id")
	private String emailId;
	private String name;
	@Column(name="date_of_birth")
	private LocalDate dateOfBirth;
	//getter and setters
}

**Sometimes, entity class has a reference of enum and if you want to persist it, you can use @Enumerated annotation.

@Entity
public class Customer {
    @Id
    @Column(name="customer_id")
    private Integer customerId;
    @Column(name="email_id")
	private String emailId;
	private String name;
    @Column(name="date_of_birth")
	private LocalDate dateOfBirth;
    @Enumerated(EnumType.STRING)
    private CustomerType customerType;
	//getters and setters
}
In the above code, CustomerType is annotated with @Enumerated annotation. The EnumType property is used to specify how the enum should be persisted in the database.

The possible values of EnumType property are as follows: 
@Enumerated(EnumType.STRING) specifies that the enum will be written and read from the corresponding database column as a String. So, customerType set to SILVER will be persisted as SILVER. 

@Enumerated(EnumType.ORDINAL) specifies that the enum will be persisted as an integer value. So, customerType set to SILVER will be persisted as 0 and customerType set to GOLD will be persisted as 1. The default is EnumType.ORDINAL.

**Best Practices in defining an Entity class

One of the best practices that should be followed while creating an Entity class is:
Override the equals() and hashCode() methods in the entity classes
The Customer entity class implementation shown to you is sufficient to map it to CUSTOMER table but it is considered as a good practice to override the equals() and hashCode() methods in the entity classes as shown below:

@Entity
public class Customer {
    @Id
    @Column(name="customer_id")
    private Integer customerId;
    @Column(name="email_id")
	private String emailId;
	private String name;
	@Column(name="date_of_birth")
	private LocalDate dateOfBirth;
    @Enumerated(EnumType.STRING)
    private CustomerType customerType
	//getter and setters
	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((this.getCustomerId() == null) ? 0 : this.getCustomerId().hashCode());
		return result;
	}
	
	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Customer other = (Customer) obj;
		if (this.getCustomerId() == null) {
			if (other.getCustomerId() != null)
				return false;
	}
}
NOTE : The implementation of equals() and hashCode() methods will change based on the requirements.

Why overriding equals() and hashCode() methods in entity class is considered to be a good practice?

This is because each instance of an entity class represents a row in database and in real time, you may have many objects of this class. So, you need to differentiate one object from the other. JPA ensures that there is a unique instance for each row of the database but if you want to store the objects in a Set, then you need to override equals() and hashCode() methods.

You know that instance of entity object represents a row in the database table but you should be able to perform database operations using entity objects. For this, JPA provides EntityManager interface. You can use methods of this interface for interacting with database using entity objects. The group of objects of entity classes managed by an EntityManager is called as persistence context. Every instance of EntityManager has its own persistence context. At any time within the same EntityManager, there should exist only one entity object with the same primary key value.

Every entity object has a state in relation to both persistence context and the database. Every entity object has different states depending on its relationship with persistence context. This defines the life cycle of an entity object. The different states of an entity object during its life cycle are as follows:

New/Transient State : A newly created entity object which has no persistence context associated with it and having no row associated with it in a table in database is said to be in new or transient state.

Managed/Persistent State : An entity object which has a persistence context and an identifier value associated with it is said to be in managed or persistent state. It may or may not have a row associated with it in a table.

Removed State : An entity object which has a row associated with it in a table and associated with a persistence context, but marked for deletion from the database is said to be in removed state.

Detached State : An entity object which is no longer associated with a persistence context with which it was previously associated with it is said to be in detached state. This usually happens when session gets closed or the object was evicted from the persistence context.

You will now have a look at some of the important methods of the EntityManager interface.
void persist(Object entity) - It makes a new entity object managed. When transaction is committed, a new row will be inserted in the database.

find(Class entityClass, Object primaryKey) - It searches the database table based on the primaryKey and returns the row as an object of entity class. It returns null if no row is present in the database. It returns the entity object in the managed state. 

void remove(Object entity) - It changes the state of entity object from managed to removed and object gets deleted from the database when transaction is committed.

void detach(Object entity) - It detaches the given entity from the persistence context associated with it and changes its state to detached.

Database Properties :

spring.datasource.url : This property defines the JDBC URL of the database.

spring.datasource.username : This property defines the login username of the database.

spring.datasource.password : This property defines the login password of the database.

spring.jpa.show-sql : This property if set to true enables logging of generated SQL statements.

spring.jpa.properties.hibernate.format_sql : This property if set to true formats generated SQL statements in pretty format.

**A best practice that should be followed with respect to @PersistenceContext is:

@PersistenceContext should be preferred over @Autowired for injecting EntityManager
@Repository(value = "customerRepository")
public class CustomerRepositoryImpl implements CustomerRepository {
	@PersistenceContext
	private EntityManager entityManager;
	// rest of the code
}

Why should @PersistenceContext should be used?
When multiple clients call the application, each call creates a unique thread.
Persistence Context is specifically designed so as to create unique EntityManager for every thread whereas Autowired creates the same Entity Manager for all the threads. This can become a design flaw as multiple clients may access the same entity.

Thus, as a best practice, always use @PersistenceContext for injecting the EntityManager.

**Advantages of Spring ORM

Spring-ORM provides support for many persistence technologies such as JPA, Hibernate, etc. It provides easy integration of these technologies with Spring and provides following benefits in development of persistence layer:

1. Easy testing 

The DAO layer code has many dependencies such EntityManager instance, JDBC DataSource instance, transaction managers etc. Using Spring’s dependency injection, you can easily replace these dependencies which makes testing of DAO layer code easy and possible without deploying it on server.

2. Common data access exceptions

The exceptions thrown from DAO layer are persistence technology specific. This means that if you change persistence technology then service layer code for handling exception needs to be changed. But by annotating DAO class with @Repository annotation the exceptions specific to underlying persistence technology are translated to Spring’s DataAccessException. So even if you change persistence technology the DAO layer always throws DataAccessException due to exception translation and thus service layer code need not to be changed. So, you can use different persistence technology in DAO layer.

3. Automatic resource management

Spring automatically manages resources such as EntityManager instance, DataSource instance and injects it into DAO layer beans and so developer need not to manage it manually.

4. Easy transaction management

Spring provide support for transaction management either declaratively or programatically without depending on persistence technology used.

**Query interface

To create and execute JPQL queries, JPA provides Query interface. An object of Query interface is created through EntityManager interface using createQuery method as follows:
Query query = entityManager.createQuery("SELECT c FROM Customer c");
where SELECT c from CustomerEntity c is a JPQL query.

This interface provides following methods to execute a query:
List getResultList() : This method executes select queries and returns a List of results. It throws IllegalStateException if called for update and delete queries.
Integer executeUpdate() : This method executes update and delete queries. It returns the number of rows updated or deleted. It throws IllegalStateException if called for select queries.
Object getSingleResult() : This method executes select query which returns a single result. If no result available it throws NoResultException. It throws NonUniqueResultException if query returns more than one results and IllegalStateException if called for update and delete queries.

There are following two ways using which you can define parameters:

**1. Named Parameters

The input parameters which are prefixed with a colon (:) are called as named parameters. For example, in following JPQL query: customerId is a named parameter:

SELECT c FROM Customer c WHERE c.customerId = :customerId;
Before executing above query values for input parameters must be set. This can be done using setParameter() method of Query interface as follows:

List<Customer> getCustomerDetails(Integer customerId){ 
     Query query = entityManager.createQuery("SELECT c FROM Customer c WHERE c.customerId = :customerId";
     query.setParmeter("customerId",customerId);
     List results = query.getResultList(;
     //rest of the code
}
In the above code, the setParameter() method accepts the name of the named parameter as first argument and the value which has to be assigned to the named parameter as the second argument.

**2. Positional Parameters

The input parameters which are prefixed with a question mark (?) followed the numeric position of the parameter in the query starting from 1 are called as positional parameters. For example, in following JPQL query customerId is mentioned as a positional parameter:

select c FROM Customer c WHERE c.customerId = ?1;
The values for positional parameters can be set by calling the setParameter() method as follows:

List<Customer> getCustomerDetails(Integer customerId){ 
     Query query = entityManager.createQuery("SELECT c FROM Customer c WHERE c.customerId = ?1";
     query.setParmeter(1,customerId);
     List results = query.getResultList();
     //rest of the code
}
In the above code, the setParameter() method accepts the numeric position of the parameter in the query as the first argument and value which has to be set to a parameter as the second argument.

JPQL provides operators for performing comparison which is similar to comparison operators of SQL. These operators can be combined with AND, OR and NOT logical operators to create complex expressions. Some of the JPQL operators are as follows:

1.Equal (=)

The following JPQL query fetches details of those customers whose customerId is 1002:

SELECT c FROM Customer c WHERE c.customerId = 1002
2. Not equal (!=)

The following JPQL query fetches details of those customers whose city is not Seattle.

SELECT c FROM Customer c WHERE c.city != 'Seattle'
3. Greater than(>)

The following JPQL query fetches details of those customers who are born after 1-Jan-1980.

SELECT c FROM Customer c WHERE c.dateOfBirth > '1-Jan-1980'
4. Greater or equal then (>=)

The following JPQL query fetches details of those customers who are born on or after 1-Jan-1980.

SELECT c FROM Customer c WHERE c.dateOfBirth >= '1-Jan-1980'
5. Less than (<)

The following JPQL query fetches details of those customers who are born before 1-Jan-1980.

SELECT c FROM Customer c WHERE c.dateOfBirth < '1-Jan-1980'
6. Less than or equal to (<=)

The following JPQL query fetches details of those customers who are born on or before after 1-Jan-1980.

SELECT c FROM Customer c WHERE c.dateOfBirth <= '1-Jan-1980'
7. BETWEEN

This operator checks whether the result of an expression is within an inclusive range of values. The following JPQL query fetches details of those customers who are born after 1-Jan-1975 but before 1-Jan-1980.

SELECT c FROM Customer c WHERE c.dateOfBirth BETWEEN '1-Jan-1975' AND '1-Jan-1980'
8. LIKE

The following JPQL query fetches details of those customers whose name starts with R:

SELECT c FROM Customer c WHERE c.name LIKE 'R%'
9. IS NULL

The following JPQL query fetches details of those customers who are not having email id:

SELECT c FROM Customer c WHERE c.emailId IS NULL
You can negate the operator with NOT to restrict the query results.

10. IN

The following JPQL query fetches details of those customers whose city is either Seattle or Vancouver:

SELECT c FROM Customer c WHERE c.city IN ('Seattle','Vancouver')

11. IS EMPTY

It restricts the query result to those entities which don’t have any associated entities. The following JPQL query returns those customers which are not having any loans:

SELECT c FROM Customer c WHERE c.loans IS EMPTY
You can negate this operator (IS NOT EMPTY) to restrict the query result to all customers which are having loans as follows:

SELECT c FROM Customer c WHERE c.loans IS NOT EMPTY
12. SIZE

It restricts the query result on the basis of number of associated entities. The following JPQL query returns those customers which are having more than two loans:

SELECT c FROM Customer c WHERE SIZE(c.loans) > 2

**JPA provides following aggregate functions:

The following query returns the average balance of a account:

SELECT AVG(a.balance) FROM Account a
The following query returns the total balance of all the accounts:

SELECT SUM(a.balance) FROM Account a
The following query returns the total number of account:

SELECT COUNT(a) FROM Account a
The following query returns the minimum balance of account:

SELECT MIN(a.balance) FROM Account a
The following query returns the maximum balance of account:

SELECT MAX(a.balance) FROM Account a

The GROUP BY clause is used for defining query results into groups. For example, the following query groups the customers by their city and returns the number of customers per city:

SELECT c.city, COUNT(c) FROM Customer c GROUP BY c.city
The HAVING clause is used with the GROUP BY clause to filter the groups. For example, the following query groups customers by their city and returns the number of customers in city Seatle or Vancouver:

SELECT c.city, COUNT(c) FROM Customer c GROUP BY c.city HAVING c.city IN ('Seatle','Vancouver')
The ORDER BY clause is used to sort the query results. To sort query results give one or more attributes of entity class according to which results have to be sorted after ORDER BY clause. By default results are sorted in ascending order. To sort the results in descending order DESC keyword is used. The following JPQL query selects all customers in the ascending order of their name:

SELECT c FROM Customer c ORDER BY c.name ASC

***Spring Data

Spring Data provides repositories which are interfaces associated with entity and provide different methods for performing database operations. Spring automatically generates the implementation class of these interfaces and provides default implementation of the methods. To use these repositories, you have to define your own repository for an entity class by extending Spring Data repositories. For example, to perform database operations on Customer entity class, you can create a CustomerRepository as follows:

public CustomerRepository extends CrudRepository<Customer, Integer>{
}
In the above code, CrudRepository interface is provided by Spring Data which accepts the entity class and data type of the identifier attribute of the entity class as generic parameters. This CrudRepository class provides methods to perform different database operations using entity class. Spring Data automatically generates the implementation class at runtime and provides default implementation of methods of CrudRepository interface for CustomerEntity class. S,o you can directly use CustomerRepository in your service class as shown below:

public class CustomerServiceImpl implements CustomerService{
  @Autowired
  CustomerRepository customerRepository;
 
  // add customer details
  public void addCustomer(Customer customer){
      customerRepository.save(customer);
  }
  // rest of the methods
}
In the above code, the implementation of save() method is automatically generated to save Customer entity objects. 

Similarly, if you have Product entity class, you can define ProductRepository as follows:

public ProductRepository extends CrudRepository<Product,Integer>{
}
So, there is no need to implement the code to perform database operations on Product entity. Now you can see that Spring Data reduces the effort required to develop the persistence layer by avoiding repetitive code. It makes the implementation of persistence layer easier and faster.

Spring Data is a high-level project from Spring whose objective is to unify and ease the access to different types of data access technologies including both SQL and NoSQL databases. It simplifies the data access layer by removing the repository (DAO) implementations entirely from your application. Now, the only artifact that needs to be explicitly defined is the interface. 

Spring Data contains a core project and many sub-projects.
Core Project provides concepts applicable to all Spring Data projects. It contains Spring Data Commons. It contains interfaces which are technology independent and supports commonly used database operations.

Sub Projects provide support for most of the data access technologies.

Spring Data contains repositories which are interfaces using which you can interact with database. We will first discuss about 2 repositories - Repository and CrudRepository.
1. Repository<T, ID> 

It is core interface of Spring Data Commons and any class which interacts with database using Spring Data must implement this interface.
It takes the entity class and the data type of its identifier as type arguments.

2. CrudRepository<T, ID> 

It extends Repository interface.
It takes the entity class and the data type of its identifier as type arguments.
It provides methods for basic CRUD operations.
To use it, you need to create an interface by extending CrudRepository. There is no need to write the implementation class as it is automatically generated at runtime.

The methods of CrudRepository are transactional by default. It gets annotated with @Transactional annotation with default values when implementation class is generated at runtime. For read operation, readOnly flag of @Transaction is set to true. You can also override default transactional settings of any of its method by overriding that method in your repository interface and annotating it with @Transactional annotation with required configuration.

**Now, if you need to fetch the details of customer based on emailId, how do you implement?

The methods provided by CrudRepository operate on primary key attribute whereas in this case, emailId is not the primary key.
So, there can be situations and requirements similar to the one discussed here where you will not have methods present in Spring Data repositories.
For implementing these type of requirements, Spring Data provides the following approaches:

1. Query creation based on the method name
2. Query creation using @Query annotation
3. Query creation using @NamedQuery annotation

***Query creation based on the method name

In this approach, we add methods in the interface which extends CrudRepository to implement custom requirements and Spring Data automatically generates the JPQL query based on the name of methods. These methods are called as query methods and are named according to some specific rules. Some basic rules for naming these methods are as follows :

1. The method name should start with "find...By", "get...By", "read...By", "count..By" or "query...By" followed by search criteria. The search criteria is specified using attribute name of entity class and some specified keywords. For example, to search customer based on emailId, the  following query method has to be added to repository interface 

Customer findByEmailId(String emailId);

When this method is called, it will be translated to the following JPQL query:

select c from Customer c where c.emailId = ?1
The number of parameters in query method must be equal to number of search conditions and they must be specified in the same order as mentioned in search conditions.

2. To fetch data based on more than one condition, you can concatenate entity attribute names using And and Or keywords to specify search criteria. For example, to search customers based on email address or name , the following method has to be added to repository interface :

List<Customer> findByEmailIdOrName(String emailId, String name);

When the above method is called, it will be translated to following JPQL query:

select c from Customer c where c.emailId = ?1 or c.name=?2
3. Some other keywords that can be used inside query method names are Between, Is, Equals, Not, IsNot, IsNull, IsNotNull, LessThan, LessThanEqual, GreaterThan, GreaterThanEqual, After, Before, Like, etc.

4. To sort the results by a specified column, you can use OrderBy keyword. By default, results are arranged in ascending order. For example, the following method searches customers by name and also orders the result in ascending order of date of birth: 

List<Customer> findByNameOrderByDateOfBirth(String name);
For sorting the results in descending order you can use Desc keyword as follows:

List<Customer> findByNameOrderByDateOfBirthDesc(String name);
5. To limit the number of results returned by a method, add First or Top keyword before the first 'By' keyword. To fetch more than one result, add the numeric value to the First and the Top keywords. For example, the following methods returns the first 5 customers whose emailId is given as method parameter: 

List<Customer> findFirst5ByEmailId(String emailId);
List<Customer> findTop5ByByEmailId(String emailId);
	
***Query creation using @Query annotation

You have learnt how to create JPQL queries using name of query method but for complex requirements, query method names become too long which make them difficult to read and maintain. So, Spring Data provides an option to write custom JPQL queries on repository methods using @Query annotation.

For example, suppose you want to fetch customer name based on emailId, then this requirement can be implemented using @Query annotation as follows:

public interface CustomerRepository extends CrudRepository<Customer, Integer> {
	//JPQL query
	@Query("SELECT c.name FROM Customer c WHERE c.emailId = :emailId")
	String findNameByEmailId(@Param("emailId") String emailId); 
}

In above code, "SELECT c.name FROM Customer c WHERE c.emailId = :emailId" is JPQL query with named parameter "emailId". The @Param("emailId") parameter defines the named parameter in the argument list. You can also use positional parameter instead of named parameter as follows:

public interface CustomerRepository extends CrudRepository<Customer, Integer> {
	//JPQL query
	@Query("SELECT c.name FROM Customer c WHERE c.emailId = ?1")
	String findNameByEmailId(String emailId); 
}

Executing update and delete queries using @Modifying annotation

You can also execute update, delete or insert operations using @Query annotation. For this, query method has to be annotated with @Transactional and @Modifying annotation along with @Query annotation. For example, the following method updates the emailId of a customer based on customer's customerId.

public interface CustomerRespository extends CrudRepository<Customer, Integer> {
	//JPQL query
    @Query("UPDATE Customer c SET c.emailId = :emailId WHERE c.customerId = :customerId")
	@Modifying
	@Transactional
	Integer updateCustomerEmailId(@Param("emailId") String updateCustomerByEmailId, @Param("customerId") Integer customerId);
}
The @Modifying annotation triggers @Query annotation to be used for update or delete or insert operation instead of a select operation.

**Query creation using @NamedQuery annotation

You have learnt how to generate query using query method name and how to define JPQL query using @Query annotation. In these approaches, the queries are scattered across different classes and the same queries may be written in different classes again and again which makes queries difficult to manage.

So, you can use named queries also with Spring Data. The named queries are queries with a name and are defined in entity classes using @NamedQuery annotation. Using Spring Data, default naming strategy of named query is to start with entity class name followed by dot (.) operator and then the name of the invoked repository method. For example, if CustomerEntity is entity class and findNameByEmailId is the name of repository method then query name will be as follows:

Customer.findNameByEmailId
Now, to associate JPQL query "SELECT c.name FROM CustomerEntity c WHERE c.emailId = :emailId" with the query name given above, the @NamedQuery annotation is used as follows:

@Entity
@Table(name="customer")
@NamedQuery(name="Customer.findNameByEmailId", query="SELECT c.name FROM Customer c WHERE c.emailId = :emailId")
public class Customer {
	@Id
	private Integer customerId;
	private String emailId;
	private String name;
	private LocalDate dateOfBirth;
	@Enumerated(value=EnumType.STRING)
	private CustomerType customerType;
    // getter and setter methods
}
To use this named query in a repository, you need to add a query method in repository interface with the same name as defined in named query and the return type of method should be specified according to named query. For example, the following method has to be added to repository to invoke named query CustomerEntity.findNameByEmailId.

public interface CustomerRespository extends CrudRepository<Customer, Integer>{
	String findNameByEmailId(@Param("emailId") String emailId); 
	}
	
***Spring Data JPA

Spring Data JPA is one of the sub project of the Spring Data which makes it easy to connect with relational databases using JPA based repositories by extending Spring Data repositories.

It provides the following interfaces:

**JpaRepository<T, ID> interface

It represents JPA specific repository.
It inherits methods of CrudRepository and PaginationAndSortingRepository. It also provides some additional methods for batch deletion of records and flushing the changes instantly to database.
JpaRepository is tightly coupled with JPA persistence technology. So use of this interface as base interface is not recommended. This is generally used used if JPA specific functionalities such as batch deletion and instant flushing of changes to database is required.
 
**JpaSpecificationExecutor interface

It is not a repository interface.
It provides methods that are used to fetch entities from database using JPA Criteria API.

***************Pagination Using Sorting***********************************

In pagination, all the records are not fetched at same time, rather a subset of records is fetched first and then the next subset of records in fetched if required and so on. This subset of records is called as Page. Every page has two fields – the page number and page size. The page number represents the individual subset of records and page size represents the number of records in a page.
Also, the customers might want to view the transactions in a specific order. For example, the customer might want to view the transactions in increasing order of transaction date. This can be implemented by sorting the records.This interface extends CrudRepository interface and provides the following methods for supporting pagination and sorting:

**Page<T> findAll(Pageable pageable)

This method returns a Page containing entities and accepts a Pageable object as parameter.
Page interface contains information about the content of the page and some other information such as total number of pages, current page number and whether the current is first page or last page. Some important methods are as follows:
1. List<T> getContent() : returns the content of a page as List
2. boolean hasContent() : returns true if page has content else returns false

Pageable interface contains information about pagination such as size and page number. It is implemented by PageRequest class. You can create its objects using the following methods of PageRequest class: For pagination of transaction details, you need to create the following TransactionRepository interface by extending PagingAndSortingReository interface.

public interface TransactionRepository extends PagingAndSortingRepository<TransactionEntity, Integer>{
}
Since this repository is extending PagingAndSortingReository repository, so findAll(Pageable pageable) is available to this interface. 

Suppose you want to fetch details of 5 transactions first, then next 5 and so on. To implement this requirement, you need to create an object of Pageable with page number = 0 and page size = 5, pass it to findAll() method and then invoke getContent() method as follows:

Pageable firstPagewithFiveRecords = PageRequest.of(0,5);
Page<CustomerEntity> customers = customerRepository.findAll(firstPageWithFiveRecords);
List<TransactionEntity> entityList = customers.getContent();
	
Now for accessing the next page, create an object of Pageable with page number = 1 and page size = 5 , pass it to findAll() method and then invoke getContent() method:

Pageable secondPagewithFiveRecords = PageRequest.of(1,5);
Page<CustomerEntity> customers = customerRepository.findAll(secondPageWithTwoElements);
List<CustomerEntity> entityList = customers.getContent();
	
Now suppose, you want to fetch the details of all transactions performed after a specified date and also want to do paging, then you can add query method to CustomerRepository as follows:

public interface TransactionRepository extends PagingAndSortingRepository<TransactionEntity, Integer>{
	public List<TransactionEntity> findByTransactionDateAfter(LocalDate transactionDate, Pageable pageable);
}

In above repository, findByTransactionDateAfter() query method also accepts Pageable object as parameter for paging. If you want to fetch the details of transactions which are performed after 29-Jan-1996 and also implement paging with page size = 2, then, you need to create a Pageable object with page number = 0 and page size = 2, pass it to findByTransactionDateAfter() method along with the transaction date as follows:

LocalDate transactionDate = LocalDate.of(1996, 1, 29);
Pageable firstPagewithTwoRecords = PageRequest.of(0,2);
Pageable pageable = PageRequest.of(pageNo, pageSize);
List<TransactionEntity> entityList = transactionRepository.findByTransactionDateAfter(transactionDate,pageable);
1. static PageRequest of(int page,int size) : create pageRequest object without any information about sorting
2. static PageRequest of(int page, int size, Sort sort) : create pageRequest object along with information about paging and sorting.

** Iterable<T> findAll(Sort sort)

This method returns all the entities in sorted order.
It accepts object of Sort class as parameter. This object contains information about the fields on which sorting is done and direction of sorting, i.e., ascending or descending. 
Spring Data provides support for pagination and sorting through the PagingAndSortingRepository repository interface.

********************************Sorting with Spring data***********************************

For sorting the transaction details, you need to create the following TransactionRepository interface by extending PagingAndSortingReository interface as follows:

public interface TransactionRepository extends PagingAndSortingRepository<TransactionEntity, Integer>{
}
Since this repository is extending PagingAndSortingReository repository, so findAll(Sort sort) is available to this interface for sorting. 

The findAll(Sort sort) method accepts object of Sort class as parameter. This class provides different ways to sort data using single and multiple columns and direction of sorting, i.e., ascending or descending using different methods. Some important methods of the Sort class are as follows:
1. public static Sort by(String... properties)
2. public Sort descending()
3. public Sort ascending()

To sort the transaction details using transaction date, you need to first create an object of Sort class using by() method with transactionDate as parameter value and then pass the Sort object to findAll() method as follows:

// Sorted by 'transactionDate' attribute in ascending order 
Sort sort = Sort.by("transactionDate");
Iterable<CustomerEntity> customers = transactionRepository.findAll(sort);
To sort the details in descending order of transaction date, you need to use the descending () method as follows:

// ordered by 'transactionDate' attribute in descending order 
Sort sort = Sort.by("transactionDate").descending();
Iterable<CustomerEntity> customers = transactionRepository.findAll(sort);
You can also sort by transaction date and then by transaction amount as follows:

// ordered by 'transactionDate' attribute in descending order 
Sort sort = Sort.by("transactionDate").descending().and(Sort.by("transactionAmount"));
Interable<CustomerEntity> customers = transactionRepository.findAll(sort);
If you want to find transaction details after a specific transaction date and sorted based on transaction date along with pagination, then you have to add query method to TransactionRepository as follows:

public interface TransactionRepository extends PagingAndSortingRepository<TransactionEntity, Integer>{
	public List<TransactionEntity> findByTransactionDateAfter(LocalDate transactionDate, Pageable pageable);
}
If you want to fetch the details of transactions which are performed after 29-Jan-1996 and sorted based on transaction date with page size = 2, then, you need to create an object of Sort using by() method with transactionDate as parameter value. Then, you need to create Pageable object with page number = 0, page size = 2  and object of Sort class and pass this Pageable object to findByTransactionDateAfter() method along with transaction date as follows:

LocalDate transactionDate = LocalDate.of(1996, 1, 29);
Sort sort = Sort.by("transactionDate");
Pageable pageable = PageRequest.of(0,2,sort);
List<TransactionEntity> transactionList = transactionRepository.findByTransactionDateAfter(transactionDate, pageable);

****************************Primary Key Generation Strategies*******************************

Consider a requirement where an admin should be able to add the customer details to the database and customer id should be automatically generated.

To implement this requirement, some mechanism is required so that the primary key gets generated automatically. JPA provides different strategies using which primary key values can be generated automatically. The different strategies provided by JPA are as follows:
1. IDENTITY strategy
2. TABLE strategy
3. SEQUENCE strategy
4. AUTO strategy
Note: These strategies are not supported by all databases. Due to this, we will learn the IDENTITY and TABLE strategy using the MySQL database and AUTO and SEQUENCE strategy using the Oracle database.

*****Identity Strategy***********

In this strategy, the value of the primary key is generated using the identity column of the table. The identity column of a table is an integer column having a primary key or unique constraint with the AUTO_INCREMENT feature. For example, in following customer table customer_id is an identity column:

CREATE TABLE customer (
	customer_id int AUTO_INCREMENT,
	email_id varchar(20),
	name varchar(10),
	date_of_birth date,
	constraint ps_customer_id_pk primary key (customer_id)
);
In this strategy, if there are no rows in the table, the starting value of the identity column will be 1, and it will be incremented by 1 for each new row added. If the rows are already present in the table, then the maximum value from the identity column is taken and incremented by 1 to generate the next primary key value.

To use this strategy, the primary key attribute of the entity class is annotated with @GeneratedValue annotation with GenerationType.IDENTITY as the value of the strategy attribute along with @Id annotation. The following code snippet shows how @GeneratedValue annotation is used to generate values for customerId:

@Entity
public class Customer{
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Integer customerId;
	
    // rest of the code
}
When you persist a new Customer entity, JPA selects customer_id column of customer table to generate the primary key value. If no rows are present in customer table then the generated value of customerId = 1. If rows are already present is customer table and maximum value present in customer_id column is 5 then the generated value of customerId = 6.

***********Table Strategy******************
In this strategy, a database table is used to generate primary key values. 

To use this strategy, the primary key attribute of entity class is annotated with @GeneratedValue annotation with GenerationType.TABLE as the value of the strategy attribute along with @Id annotation. The following code snippet shows how @GeneratedValue annotation is used to generate values for customerId:
When you persist a new Customer entity, JPA selects the next primary key value from the next_val column of following hibernate_sequences table:

@Entity
public class Customer{
    @Id
    @GeneratedValue(strategy=GenerationType.TABLE)
    private Integer customerId;
    //rest of the code @TableGenerator annotation specifies that the following id_gen table is used for generating primary key values:

@Entity
public class Customer{
	@Id
	@TableGenerator(
            name="pkgen", 
            table="id_gen", 
            pkColumnName="gen_key", 
            valueColumnName="gen_value",
            pkColumnValue="cust_id",
            allocationSize=1)
	@GeneratedValue(generator="pkgen",strategy=GenerationType.TABLE)
	private Integer customerId;
    //rest of the code
}
The @TableGenerator annotation has the following attributes:

name : It specifies the name of the generator. This name is used to access the generator inside the entity class.

table : It specifies the name of the table which has to be used for generating primary key values. In our case the table is id_gen.

pkColumnName : It is the name of the primary key column which contains generator names used for generating primary key value. In our case pkColumnName is gen_key.

valueColumnName : It is the name of the column which contains the last primary key value generated. In our case valueColumnName is gen_value.

pkColumnValue : In table, many generators are may be present. This attribute specifies which generator has to be used for generating primary key value among a set of generators in the table. In our case pkColumnValue is cust_id.

allocationSize : It specifies the amount to increment by when allocating id numbers from the generator.

@SequenceGenerator annotation. Then a reference of this sequence generator is used in @GeneratedValue annotation. For example, the following code snippet shows how custom database sequence can be used:

@Entity
public class Customer{
	@Id
    @SequenceGenerator(name="pkgen",sequenceName="customer_sequence",allocationSize=1)
	@GeneratedValue(generator="pkgen",strategy=GenerationType.SEQUENCE)
	private Integer customerId;
	// rest of the code
}
In the above code, @SequenceGenerator annotation defines a sequence generator with the name "pkgen" which references database sequence named "customer_sequence" and the @GeneratedValue annotation references this using generator attribute. The customer_sequence sequence must be present in the database. If it is not present you must create it.
}
In this table the value in sequence_name column is default and the next_val column contains the next value of the primary key. If it is not present you must create it. For example, if the value in the next_val column is 5 and you persist a new Customer entity object then customer details with customerId as 5 will be persisted in the customer table and the value in the next_val column will be updated to 6.

*****Auto Strategy***

In this strategy, the persistence provider automatically selects an appropriate strategy for generating primary key values depending on the database used. If For example, if Hibernate 5.0 is a persistence provider and MySQL database is used then this strategy selects the TABLE strategy (hibernate_sequences table) for generating primary key values otherwise it uses SEQUENCE strategy (hibernate_sequence sequence).  To use this strategy, the primary key attribute of an entity class is annotated with @GeneratedValue annotation with GenerationType.AUTO as the value of the strategy attribute along with @Id annotation as follows:

@Entity
public class Customer {
   @Id 
   @GeneratedValue(strategy=GenerationType.AUTO)
   private int customerId;
   //rest of the code 
}

*******************Cardinality***********************

The cardinality of a relationship defines how many entities exist on each side of the same relationship instance. In our Customer and Address example, one customer can have many addresses, so the cardinality of the Customer side is one, and the Address side is many. On the basis of cardinality, we have the following types of relationships:
One – to – One Relationship
One – to – Many Relationship
Many – to – One Relationship
Many – to – Many Relationship

@Entity
public class Customer {
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Integer customerId;
	private String emailId;
	private String name;
	private LocalDate dateOfBirth;
	@OneToOne(cascade = CascadeType.ALL)
	@JoinColumn(name = "address_id", unique = true)
	private Address address;
	//getter and setter
}

In the above code the reference of Address in annotated with @OneToOne annotation which declares that there is a one-to-one relationship between Customer and Address entity classes. The @JoinColumn annotation is used to define the name of the foreign key column in the Customer table that links the customer to the address. Now let us understand these annotations in detail:

@OneToOne(cascade = CascadeType.ALL)

This annotation specifies that the association relationship has one-to-one multiplicity.
the cascade attribute specifies operations performed on the owner entity that must be transferred or cascaded to the target entity. It takes values of type CascadeType enumeration. The values of this enumeration are PERSIST, REFRESH, REMOVE, MERGE, DETACH, and ALL. The value ALL specifies that all operations performed on Customer will be cascaded to Address. By default, none of the operations will be cascaded.

@JoinColumn(name = "address_id", unique = true)
This annotation is used to specify the foreign key column that joins the owner and target entity.
The name attribute specifies the name of the foreign key column in the table mapped to the owner entity.
The unique = true assures that unique values will be stored in the join column.

The Loan entity class is the owner's side of relationship and is mapped with Loan table. This has a reference of Customer entity class as shown below :
@Entity
public class Loan{
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Integer loanId;
	private Double amount;
	private LocalDate issueDate;
	private String status;
	@ManyToOne(cascade=CascadeType.ALL)
	@JoinColumn(name="cust_id")
	private Customer customer;
	
    //getter and setter methods
}
In the above code the reference of Customer entity class in annotated with @ManyToOne annotation which declares that there is many-to-one relationship between Customer and Loan. The @JoinColumn annotation is used to define the name of the foreign key column in the Customer table that links the customer to the card. Now let us understand these annotations in detail:

@ManyToOne(cascade = CascadeType.ALL)

This annotation indicates that the relationship has many-to-one cardinality. 
The cascade attribute tells which operation (such as insert, update, delete) performed on source entity can be transferred or cascaded to target entity. By default, none of the operations will be cascaded. It takes values of type CascadeType enumeration.  The value ALL means all operations will be cascaded from source to target. Other values of this enumeration are PERSIST, REFRESH, REMOVE, MERGE, and DETACH.

@JoinColumn(name = "cust_id")
This annotation is used to define the name of the foreign key column that joins the owner and target entity.
The name attribute specifies the name of the foreign key column in the table mapped to the source entity.

@Entity
public class Customer{
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	private Integer customerId;
	private String emailId;
	private String name;
	private LocalDate dateOfBirth;
	@OneToMany(cascade=CascadeType.ALL)
	@JoinColumn(name="cust_id")
	private List<Card> cards; 
    //getter and setter methods
}
	
In Customer entity class reference of List<Card> in annotated with @OneToMany annotation which declares that there exists a one-to-many relationship between Customer and Card entity classes. The @JoinColumn annotation is used to give the name of a foreign key column in Card table which links the customer to the card. You can also use Set<Card> instead of List<Card>. Now let us understand these annotations in detail:

@OneToMany(cascade = CascadeType.ALL)

This annotation indicates that the relationship has one-to-many cardinality. 

The cascade attribute tells which operation (such as insert, update, delete) performed on the source entity can be transferred or cascaded to the target entity. By default, none of the operations will be cascaded. It takes values of type CascadeType enumeration.  The value ALL means all operations will be cascaded from source to target. Other values of this enumeration are PERSIST, REFRESH, REMOVE, MERGE, and DETACH.

@JoinColumn(name = "cust_id")

This annotation is used to specify the join column using its name attribute. The target entity is mapped to the Customer table which has cust_id as the foreign key column, so the name is cust_id.

****************Challenges in using JDBC**********************
You have already learned how to use JDBC for data access. But using JDBC is challenging because of following reasons:

Connection Management : In JDBC you have to write code for managing connections. If connections are not managed properly then application performance gets impacted.
Exception Handling : The JDBC code throws SQLException which is a checked exception. So you have to write code to handle them even though database errors are mostly unrecoverable. Also, inappropriate handling of SQLException leads to many other problems. 
Code Duplication: In JDBC lot of repetitive code is written to perform operations such as obtaining database connection, creating a statement, converting data obtained as rows and columns into objects etc. Because of this lot of time is spent in writing data persistence rather business logic. This effects project costs, efforts, and timelines.
So to make use of JDBC easier Spring provided one layer of abstraction, called Spring JDBC, on top of existing JDBC technology. 

**************Spring JDBC********************
Spring provides support for JDBC through Spring JDBC. It is a layer of abstraction on JDBC which makes use of JDBC easier and helps to avoid common errors. It consists of following four packages :

org.springframework.jdbc.core – It contains core JDBC classes JdbcTemplate, SimpleJdbcCall, NamedParameterJdbcTemplate, and SimpleJdbcInsert.
org.springframework.jdbc.datasource – It contains utility classes to access database and different implementation classes of DataSource.
org.springframework.jdbc.object – It contains classes to access database in an object-oriented manner. It allows executing queries and returning the results as a model object. It also maps the query results between the columns and properties of objects.
org.springframework.jdbc.support – It contains support classes for core and object packages and provides facility for SQLException translation.
The JdbcTemplate is core class of Spring JDBC.

JdbcTemplate
JdbcTemplate is the main class of Spring JDBC. It makes use of JDBC easy in following ways:

It takes care of opening and closing of connections, so you need not to write the code for managing them. 
It provides various overloaded methods for executing SQL queries and handling results as different types of objects.
It catches JDBC exceptions and translates them to more generic and informative exception.
It provides several overloaded methods to execute SQL queries. Some methods of this class are as follows:

update() - This method is used for executing INSERT, UPDATE and DELETE queries. 
query() - This method is used for executing SELECT queries which can return multiple rows.
queryForObject() - This method is used for executing SELECT queries which returns a single row.

A JPA application is configured using an xml file named as persistence.xml.  It should be present inside META-INF folder under the classpath in a Maven project.
In this xml file we give information about JPA provider,if  Oracle database is used then properties and entity classes details are given as shown below:

<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.1"
	xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence 
    http://xmlns.jcp.org/xml/ns/persistence/persistence_2_1.xsd">
	<persistence-unit name="JPA_CRUD" transaction-type="RESOURCE_LOCAL">
		<provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>
		<class>com.infy.entity.CustomerEntity</class>
		<properties>
			<!-- Configuring JDBC properties -->
			<property name="javax.persistence.jdbc.url" value="jdbc:oracle:thin:@localhost:1521:xe" />
			<property name="javax.persistence.jdbc.user" value="system" />
			<property name="javax.persistence.jdbc.password" value="oracle" />
		</properties>
	</persistence-unit>
</persistence>

Creating Entity classes
Entity classes are Java beans which are mapped to a table in database and whose instance represents a row in database table. JPA provides annotations to create entity classes which are present in the javax.persistence package. Some of these annotations are as follows:

@Entity : It specifies that the Java bean class is an entity class.
@Id : Every entity object must have a primary key attribute which uniquely identifies it. This primary key attribute is specified using @Id annotation. Usually it is the attribute which is mapped with the primary key column of the table.
@Table : It specifies the name of the table with which entity class is mapped. By default, table name is same as class name. If table name is different from class name then this annotation is used.
@Column : It specifies the name of column in table with which attribute in entity class is mapped. By default, column name is same as attribute name. If attribute name is different from column name then this annotation is used.
@Transient : It specifies the attributes which are not stored in table.
@Enumerated : It specifies that annotated property should be persisted as a enumerated type.


During insert operation entity object will go through the following states:

When the object of an entity class is created it will be in a new state.
When persist() method is called the entity will move from new to managed state.
When the transaction is committed a new row is inserted in the table and the entity continues to be in a managed state.

During update operation entity will go through the following states:

When an entity object is retrieved using find() method the entity object is in managed state.
After updating the entity remains in the managed state.
When the transaction is committed the changes will be reflected in the table and the entity continues to be in a managed state.

uring delete operation entity object will go through the following states:

When an entity object is retrieved using find() method the entity object is in a managed state.
When remove() method is called the entity object will move from managed to removed state. Any changes made to the object in the removed state are not saved to the database.
When the transaction is committed the corresponding row will get deleted from the table and the entity moves to a new state.

So far we have learnt that an object is in the managed state when it is associated with an entity manager. An object which was previously in the managed state but now not associated with an entity manager is said to be in a detached state.  Any change in the state of the detached object will not be reflected in the database but detached objects can be re-attached with the session. An object in the managed state can be moved to a detached state using the following methods:

void detach(Object object): Removes the object from the entity manager.
boolean close(): All the objects associated with an entity manager will be removed after calling this method and it also closes the entity manager by releasing database connection.
void clear(): All the objects associated with an entity manager will be removed from the session after calling this method.


