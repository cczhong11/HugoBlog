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

