---
title: "SQL basics"
date: "2018-12-15"
description: "An introduction to SQL"
categories:
    - "db"
---

# Introduction to SQL 

SQL is a very important language we used in Relational Database management system. Writing efficient SQL query can help us do lots of things. 

## Relational model

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8d/Relational_model_concepts.png/360px-Relational_model_concepts.png)

In the beginning, I would like to talk about relational model. In short, it is a table with different columns. In a more specific definition, it is an approach to managing data using a structure and language consistent with first-order predicate logic. A relation is an unordered set and it contains the relationship of attributes. The primary key in each relation is unique and we could use it to find any relation. A tuple is a set of attribute values in the relation.

## SQL language and Relational algebra

There are several important operations in the Relational algebra and I will introduce them with SQL operations.

### Select 

This operation acts a filter, which can help us get a subset of the tuples we want. We can express the condition to filter the data with a selection predicate. We can also combine multiple predicates using AND.

In SQL, we can use the following command to execute SELECT. We put the predicates after where clause.

```SQL
SELECT * FROM table WHERE x>10;
```


### Projection

This operation can generate a relation with tuples that contains only the specified attributes. It acts as a subset operation. In is also a very common operation in the SQL

In SQL, we can use the following command to execute Projection. We put attributes after SELECT clause.

```SQL
SELECT a1,a2 FROM table;
```

### UNION/INTERSECTION/DIFFERENCE

These three operations are not very common in SQL. It can get results from two relations. 

Union generates a relation that contains all tuples that appear in either only one or both of the input relations. Intersection generates a relation that contains only the tuples that appear in both of the input relations. Difference generates a relation that contains only the tuples that appear in the first and not the second of the input relations.

```SQL

(SELECT * FROM R) UNION (SELECT * FROM S);

(SELECT * FROM R) INTERSECT (SELECT * FROM S);

(SELECT * FROM R) EXCEPT (SELECT * FROM S);
```


### PRODUCT/JOIN

The product will generate a relation that contains all possible combinations of tuples from the input relations. If we had M relations in table R and N relations in table S, we will have M*N results after the PRODUCT.

JOIN is to generate a relation that contains all tuples that are a combination of two tuples. There are also some other various kind of JOIN. Inner JOIN is the same as JOIN.

LEFT JOIN returns all records from the left table (table1), and the matched records from the right table (table2).

OUTER JOIN return all records when there is a match in either left (table1) or right (table2) table records.

```SQL
SELECT * from table1 INNER/LEFT/OUTER JOIN table2 on table1.attr1 = table2.attr2
```

## Advanced SQL
### Aggregations + Group By

There are many aggregate functions, including the following operations:

-  AVG(col) Return the average col value.  
-  MIN(col) Return minimum col value.  
-  MAX(col) Return maximum col value.  
-  SUM(col) Return sum of values in col.  
-  COUNT(col) Return # of values for col.
 
 We could also use DISTINCT keyword for COUNT, SUM, AVG.
 
 If we want to get aggregate results based on different groups, we can use `group by` to calculate aggregates against each subset. 
 
 If we want to filter the result based on aggregation computation, we can use `HAVING attr > x` after `group by`.
 
### OUTPUT control

For output redirection, we can use `INTO table` to store query result to another table. Or `CREATE TABLE A (select ....)` can do the same thing.

In order to get ORDER result, we can use `ORDER BY COL` and use `ASC|DESC` to control the order. 

We can also use `LIMIT` keyword to control the offset for output.

### Nested Queries

We can also use a query inside a clause. For example, we can put  a select query after WHERE. `select a from b where x in (select x from...)`. Here there are 4 operators we can use.

- ALL→ Must satisfy expression for all rows in subquery
- ANY→ Must satisfy expression for at least one row in sub-query.
- IN→ Equivalent to '=ANY()' . 
- EXISTS→ At least one row is returned.

### Common Table Expressions

We can use this as a temporary table. 

```sql
WITH cteSource (maxId) AS ( SELECT MAX(sid) FROM enrolled
),
X(Y) AS (SELECT ....)
SELECT name FROM student, cteSource WHERE student.sid = cteSource.maxId
```