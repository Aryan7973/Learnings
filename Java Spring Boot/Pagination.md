# Pagination and Sorting in Spring Data JPA

When dealing with large datasets, retrieving all records at once is inefficient and can affect application performance.

To handle large data effectively, we use:

- **Pagination** → Fetch data in smaller chunks (pages)
- **Sorting** → Order records by specific fields

---

# 📌 Why Pagination?

Instead of loading 1,000 records at once, we can retrieve:

- 10 records per page
- Load next page only when needed

This:
- Improves performance
- Reduces memory usage
- Enhances user experience

---

# 📌 Why Sorting?

Sorting arranges records based on one or more fields.

Examples:
- Sort products by name
- Sort products by price (ascending/descending)

---

# Repository Setup

To enable pagination and sorting:

```java
public interface ProductRepository 
        extends PagingAndSortingRepository<Product, Long> {
}
```

⚠ Note:
- `JpaRepository` already extends `PagingAndSortingRepository`
- So extending `JpaRepository` also enables paging & sorting

---

# Built-in Methods Available

Once extended, we get:

```java
findAll(Pageable pageable)
findAll(Sort sort)
```

We can also define custom methods:

```java
List<Product> findAllByPrice(double price, Pageable pageable);
```

---

# Creating Pageable (PageRequest)

`PageRequest` is an implementation of the `Pageable` interface.

### Syntax

```java
PageRequest.of(pageNumber, pageSize)
```

⚠ Page numbering starts from **0**

---

## Example

```java
Pageable firstPageWithTwoElements = PageRequest.of(0, 2);
```

---

# Fetching Paginated Data

```java
Page<Product> allProducts =
    productRepository.findAll(firstPageWithTwoElements);
```

---

## Custom Method with Pagination

```java
List<Product> allTenDollarProducts =
    productRepository.findAllByPrice(10, secondPageWithFiveElements);
```

---

# Page vs Slice vs List

When returning paginated data, we can return:

- `Page<T>`
- `Slice<T>`
- `List<T>`

---

## 🔹 Page<T>

- Contains data
- Knows total number of pages
- Executes additional `COUNT` query

### Use When:
You need total pages / total elements.

---

## 🔹 Slice<T>

- Contains data
- Only knows if next slice exists
- Does NOT execute count query

### Use When:
You only care about next page availability.

---

## 🔹 List<T>

- Only contains result data
- No pagination metadata

---

# Sorting Data

## Simple Sorting

```java
Page<Product> allProductsSortedByName =
    productRepository.findAll(Sort.by("name"));
```

---

# Sorting with PageRequest

Sorting can be combined with pagination:

```java
Pageable sortedByName =
    PageRequest.of(0, 3, Sort.by("name"));
```

---

## Descending Order

```java
Pageable sortedByPriceDesc =
    PageRequest.of(0, 3, Sort.by("price").descending());
```

---

## Multiple Fields Sorting

```java
Pageable sortedByPriceDescNameAsc =
    PageRequest.of(
        0,
        5,
        Sort.by("price")
            .descending()
            .and(Sort.by("name"))
    );
```

This means:
- First sort by price (descending)
- If price same → sort by name (ascending)

---

# Using Pageable in Spring MVC

With Spring Data Web Support enabled,  
Spring MVC can automatically inject `Pageable` into controller methods.

Example:

```java
@GetMapping("/products")
public Page<Product> getProducts(Pageable pageable) {
    return productRepository.findAll(pageable);
}
```

---

# 🔥 Interview Summary

- Pagination improves performance
- Page numbers start at 0
- `Page<T>` executes extra count query
- `Slice<T>` avoids count query
- `JpaRepository` already supports pagination
- Sorting can be standalone or combined with PageRequest

---

# ✅ Quick Recap

| Feature | Used For |
|----------|----------|
| Pageable | Pagination |
| PageRequest | Pageable implementation |
| Sort | Sorting |
| Page<T> | Full pagination info |
| Slice<T> | Lightweight pagination |
| JpaRepository | Supports paging & sorting |

---

# 🚀 Best Practice

- Use `Page<T>` when frontend needs total pages
- Use `Slice<T>` for infinite scroll
- Combine sorting inside `PageRequest`
- Avoid unnecessary count queries for performance
