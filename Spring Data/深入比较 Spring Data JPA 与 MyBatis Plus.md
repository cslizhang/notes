# 深入比较 Spring Data JPA 与 MyBatis Plus：数据设计、关系映射与自定义 SQL

在现代 Java 企业级应用开发中，对象关系映射（ORM）框架是不可或缺的工具。Spring Data JPA 和 MyBatis Plus 是两个流行的 ORM 框架，它们在处理数据持久化、关系映射和自定义 SQL 方面各有特点。本文将从数据设计、关系映射和自定义 SQL 等角度，通过实际代码示例，探讨这两种框架的差异。

## 一、数据设计

### Spring Data JPA

Spring Data JPA 遵循 JPA 规范，使用注解来映射实体和数据库表。它支持全注解方式，无需 XML 配置，使得实体类更加简洁。

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username", nullable = false, unique = true)
    private String username;

    @Column(name = "email")
    private String email;

    // getters and setters
}
```

### MyBatis Plus

MyBatis Plus 同样支持注解方式定义实体，但它更倾向于使用 XML 文件来定义 SQL 映射，这为复杂的 SQL 查询提供了更大的灵活性。

```java
@TableName("users")
public class User {
    private Long id;
    private String username;
    private String email;

    // getters and setters
}
```

## 二、关系映射

### Spring Data JPA

Spring Data JPA 支持多种关系映射注解，如 `@OneToMany`、`@ManyToOne`、`@ManyToMany` 和 `@OneToOne`，使得定义实体间的关系变得简单直观。

```java
@Entity
public class User {
    // ... other fields

    @OneToMany(mappedBy = "user")
    private List<Order> orders = new ArrayList<>();
}

@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;

    // getters and setters
}
```

### MyBatis Plus

MyBatis Plus 也支持关系映射，但通常需要在 XML 文件中定义关联查询的 SQL，或者使用注解和 Lambda 查询构造器来简化操作。

```java
@TableName("orders")
public class Order {
    private Long id;
    private Long userId;

    @TableField(exist = false)
    private User user;

    // getters and setters
}
```

## 三、自定义 SQL

### Spring Data JPA

Spring Data JPA 支持通过 `@Query` 注解来执行自定义的 JPQL 或原生 SQL 查询。

```java
public interface UserRepository extends JpaRepository<User, Long> {
    @Query("SELECT u FROM User u WHERE u.username = ?1")
    User findUserByUsername(String username);

    @Query(value = "SELECT * FROM users WHERE username = ?1", nativeQuery = true)
    User findUserByUsernameNativeQuery(String username);
}
```

### MyBatis Plus

MyBatis Plus 支持在 Mapper 接口中定义方法，并通过 XML 或注解来编写自定义 SQL。

```java
public interface UserMapper extends BaseMapper<User> {
    @Select("SELECT * FROM users WHERE username = #{username}")
    User findUserByUsername(@Param("username") String username);
}
```

## 四、总结

Spring Data JPA 和 MyBatis Plus 都是强大的 ORM 框架，它们在数据设计、关系映射和自定义 SQL 方面各有优势。

- **数据设计**：Spring Data JPA 通过 JPA 注解提供了一种声明式的数据模型映射方式，而 MyBatis Plus 提供了注解和 XML 两种方式，更灵活。
- **关系映射**：Spring Data JPA 的关系映射注解使得定义实体间关系直观且易于理解。MyBatis Plus 则需要在 XML 中定义关联查询，或者使用注解和 Lambda 查询构造器。
- **自定义 SQL**：Spring Data JPA 通过 `@Query` 注解支持自定义 JPQL 和原生 SQL 查询。MyBatis Plus 则提供了 XML 和注解两种方式来定义自定义 SQL，更加灵活。

**综合来看，Spring Data JPA 完胜 Mybatis，再加上spring 原生支持。如果新项目，建议使用Spring Data JPA。**
