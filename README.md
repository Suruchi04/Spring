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




