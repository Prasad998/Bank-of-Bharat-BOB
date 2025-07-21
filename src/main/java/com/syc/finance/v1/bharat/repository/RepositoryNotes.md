Here’s a **Spring Data JPA Query Methods Cheat Sheet** with 20+ common patterns you can keep handy.

---

# **Spring Data JPA Query Methods Cheat Sheet**

Spring Data JPA automatically implements repository methods based on **method naming conventions**. This allows you to write zero SQL for most common queries.

---

## **1. Basic Finders**

```java
User findByEmail(String email);
List<User> findByFirstName(String firstName);
```

**SQL:**

```sql
SELECT * FROM user WHERE email = ?;
```

---

## **2. AND / OR Conditions**

```java
User findByEmailAndStatus(String email, String status);
List<User> findByEmailOrPhone(String email, String phone);
```

**SQL:**

```sql
SELECT * FROM user WHERE email = ? AND status = ?;
SELECT * FROM user WHERE email = ? OR phone = ?;
```

---

## **3. Comparison Operators**

```java
List<Product> findByPriceGreaterThan(Double price);
List<Product> findByPriceLessThan(Double price);
List<Product> findByPriceGreaterThanEqual(Double price);
List<Product> findByPriceLessThanEqual(Double price);
```

**SQL:**

```sql
SELECT * FROM product WHERE price > ?;
```

---

## **4. BETWEEN**

```java
List<Order> findByOrderDateBetween(LocalDateTime start, LocalDateTime end);
```

**SQL:**

```sql
SELECT * FROM order WHERE order_date BETWEEN ? AND ?;
```

---

## **5. LIKE / Containing / StartingWith / EndingWith**

```java
List<User> findByFirstNameLike(String pattern);
List<User> findByFirstNameContaining(String part);
List<User> findByFirstNameStartingWith(String prefix);
List<User> findByFirstNameEndingWith(String suffix);
```

**SQL:**

```sql
SELECT * FROM user WHERE first_name LIKE ?;
```

---

## **6. NULL Checks**

```java
List<User> findByLastNameIsNull();
List<User> findByLastNameIsNotNull();
```

**SQL:**

```sql
SELECT * FROM user WHERE last_name IS NULL;
```

---

## **7. IN Queries**

```java
List<User> findByIdIn(List<Long> ids);
List<User> findByStatusIn(List<String> statuses);
```

**SQL:**

```sql
SELECT * FROM user WHERE id IN (?, ?, ...);
```

---

## **8. Sorting**

```java
List<Product> findByCategoryOrderByPriceAsc(String category);
List<Product> findByCategoryOrderByPriceDesc(String category);
```

**SQL:**

```sql
SELECT * FROM product WHERE category = ? ORDER BY price ASC;
```

---

## **9. Limiting (Top/First)**

```java
List<Product> findTop3ByCategoryOrderByPriceDesc(String category);
Product findFirstByOrderByPriceAsc();
```

**SQL:**

```sql
SELECT * FROM product ORDER BY price ASC LIMIT 1;
```

---

## **10. Boolean Checks**

```java
boolean existsByEmail(String email);
```

**SQL:**

```sql
SELECT CASE WHEN COUNT(*) > 0 THEN true ELSE false END FROM user WHERE email = ?;
```

---

## **11. Counting**

```java
long countByStatus(String status);
```

**SQL:**

```sql
SELECT COUNT(*) FROM user WHERE status = ?;
```

---

## **12. Delete by Field**

```java
void deleteByEmail(String email);
```

**SQL:**

```sql
DELETE FROM user WHERE email = ?;
```

---

## **13. Distinct**

```java
List<User> findDistinctByFirstName(String firstName);
```

**SQL:**

```sql
SELECT DISTINCT * FROM user WHERE first_name = ?;
```

---

## **14. Ignore Case**

```java
List<User> findByFirstNameIgnoreCase(String firstName);
```

**SQL:**

```sql
SELECT * FROM user WHERE LOWER(first_name) = LOWER(?);
```

---

## **15. Nested Properties**

```java
List<Order> findByCustomerName(String name);
```

**SQL:**

```sql
SELECT * FROM order WHERE customer_name = ?;
```

---

## **16. Date Comparisons**

```java
List<Order> findByOrderDateAfter(LocalDateTime date);
List<Order> findByOrderDateBefore(LocalDateTime date);
```

**SQL:**

```sql
SELECT * FROM order WHERE order_date > ?;
```

---

## **17. Not Operator**

```java
List<User> findByStatusNot(String status);
```

**SQL:**

```sql
SELECT * FROM user WHERE status <> ?;
```

---

## **18. Custom Queries with @Query**

```java
@Query("SELECT u FROM User u WHERE u.email = :email")
User getUserByEmail(@Param("email") String email);
```

**SQL:**

```sql
SELECT * FROM user WHERE email = ?;
```

---

## **19. Top & Limit with Dynamic Sorting**

```java
Page<User> findByStatus(String status, Pageable pageable);
```

**Usage:**

```java
PageRequest.of(0, 10, Sort.by("email").descending());
```

---

## **20. Exists Queries**

```java
boolean existsByPhone(String phone);
```

**SQL:**

```sql
SELECT CASE WHEN COUNT(*) > 0 THEN true ELSE false END FROM user WHERE phone = ?;
```

---
